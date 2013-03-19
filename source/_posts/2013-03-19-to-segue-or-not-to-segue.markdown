---
layout: post
title: "To Segue or Not to Segue"
date: 2013-03-19 10:38
comments: true
categories: iOS
---
iOS 5 introduced [Storyboards](http://www.raywenderlich.com/5138/beginning-storyboards-in-ios-5-part-1), which allow you to visually define the flow of an app. The transitions between view controllers in a Storyboard are called "Segues".

Initially, the benefits of using segues seem clear:

*	Spend less time writing boring transition code.
*	Code is cleaner and not littered with transitions.
*	You get a visual representation of how the app fits together.

However, after using segues for a while I've found there are a few downsides:

* The visual layout is restricted. Segues always exit from right of a controller and enter from  the left. For any moderately complex app, you can end up with a storyboard that resembles spaghetti.
* I almost always need to pass data to the controller I'm transitioning to. This requires dropping back to the code to intercept the segue, find the controller, then set its properties.
* I often find I end up writing quite lengthy and obscure code to intercept the segue.

Here's a comparison of transitioning using segues versus a traditional approach. In this code I'm handling tapping a detail accessory on a row in a table view, then transitioning to a view controller.

First, I'll use a segue:

{% img /images/segue1.png 789 %}

In theory I should just be able to connect a segue from the detail accessory to the next view controller and I'm done! Not quite... I also need to assign some data on the controller I'm transitioning to. So I need to intercept the segue by implementing the <code>prepareForSegue</code> method, then check the segue identifier to ensure I am handling the correct segue:

``` objective-c
- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
    if ([segue.identifier isEqualToString:@"ContactDetailSegue"]) {
        ContactDetailViewController *controller = segue.destinationViewController;
    }
}
```

So I have the controller and now I need to assign some data to it. But how do I know which row was tapped? There is nothing in UITableView that stores the selected state for an accessory view. So I'll need an instance variable to store the row index and set this in the <code>tableView:accessoryButtonTappedForRowWithIndexPath:</code> method:

``` objective-c
- (void)tableView:(UITableView *)tableView accessoryButtonTappedForRowWithIndexPath:(NSIndexPath *)indexPath {
    self.selectedDetailIndexPath = indexPath;
}
```

Now I can use this to assign data to the controller I am transitioning to:

``` objective-c
- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
    if ([segue.identifier isEqualToString:@"ContactDetailSegue"]) {
        ContactDetailViewController *controller = segue.destinationViewController;
        Contact *contact = [self.contacts objectAtIndex:self.selectedDetailIndexPath.row];
        controller.contact = contact;
    }
}
```

But hang on! The <code>tableView:accessoryButtonTappedForRowWithIndexPath:</code> method gets called <em>after</em> <code>prepareForSegue</code>, which means my <code>selectedDetailIndexPath</code> variable is not yet set. So now I need to disconnect the automatic segue from the detail accessory and instead create a <em>manual</em> segue between the two controllers.

{% img /images/segue2.png 789 %}

Next, I need to call <code>performSegueWithIdentifier</code> to manually trigger the segue in the <code>tableView:accessoryButtonTappedForRowWithIndexPath:</code> method

``` objective-c
- (void)tableView:(UITableView *)tableView accessoryButtonTappedForRowWithIndexPath:(NSIndexPath *)indexPath {
    self.selectedDetailIndexPath = indexPath;
    [self performSegueWithIdentifier:@"ContactDetailSegue" sender:self];
}
```

Phew, that ended up being quite complicated.

Now, let's look at the "traditional" way of coding a transition without segues using <code>pushViewController</code>:

``` objective-c
- (void)tableView:(UITableView *)tableView accessoryButtonTappedForRowWithIndexPath:(NSIndexPath *)indexPath {
    ContactDetailViewController *controller = [self.storyboard instantiateViewControllerWithIdentifier:@"ContactDetailViewController"];
    Contact *contact = [self.contacts objectAtIndex:indexPath.row];
    controller.contact = contact;
    [self.navigationController pushViewController:controller animated:YES];
}
```

That replaces all the previous code! Feels much simpler, doesn't it? What I like about this is that the transition code is all in one place, not spread across two methods. Sure, there's no "visual" representation of the transition, but if the code is clean and clear, it should be easy to determine how one controller transitions to the next.

So should you ditch using segues? Not necessarily. They can definitely save time if you are transitioning between static views. But there may be cases where it's cleaner and simpler to do a manual transition rather than use a segue.

Segues are a step in the right direction, the less code we have to write the better! Hopefully they'll continue to be improved in future updates of iOS. But with the current implementation, you often have to write quite obscure code to work-around the limitations.