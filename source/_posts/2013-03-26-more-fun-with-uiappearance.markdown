---
layout: post
title: "More Fun With UIAppearance"
date: 2013-03-26 20:14
comments: true
categories: iOS
---
In [my last post](/blog/2013/03/26/uiappearance-with-uiss) I discussed using UIAppearance with [UISS](http://github.com/robertwijas/UISS) to style iOS apps. Today I discovered a few more tricks with UIAppearance.

UIKit only exposes a limited set of elements that can be styled with UIAppearance. But what if we want to style something that's not exposed? Well, it turns out you can define your own UIAppearance proxies by appending a setter method declaration with <code>UI_APPEARANCE_SELECTOR</code>.

Say, for example, I want to use UIAppearance to set rounded corners on a UIView. I can create a <code>setCornerRadius</code> method on a UIView subclass that's appended with <code>UI_APPEARANCE_SELECTOR</code>:

``` objective-c
@interface MyView : UIView

- (void)setCornerRadius:(CGFloat)cornerRadius UI_APPEARANCE_SELECTOR;

@end
```

UIView itself doesn't have a <code>corderRadius</code> property, so the implementation needs to set the value on the underlying CALayer:

``` objective-c
@implementation MyView

- (void)setCornerRadius:(CGFloat)cornerRadius {
    self.layer.cornerRadius = cornerRadius;
}

@end
```

Now I can set the new <code>corderRadius</code> style on all instances of <code>MyView</code> with UIAppearance:

``` objective-c
[[MyView appearance] setCornerRadius:3];
```

Or, with [UISS](http://github.com/robertwijas/UISS) I can use JSON to set the style:

``` javascript
{
	"MyView": {
		"cornerRadius": 3
	}
}
```

Now, this is pretty neat, but what if I want to expose <code>cornerRadius</code> on every UIView in the application, not just the subclass? Well, it turns out you can create a category on UIView that defines custom UIAppearance methods:

``` objective-c
@interface UIView (Appearance)

- (void)setCornerRadius:(CGFloat)cornerRadius UI_APPEARANCE_SELECTOR;
- (void)setBorderColor:(UIColor *)borderColor UI_APPEARANCE_SELECTOR;
- (void)setBorderWidth:(CGFloat)borderWidth UI_APPEARANCE_SELECTOR;

@end

@implementation UIView (Appearance)

- (void)setCornerRadius:(CGFloat)cornerRadius {
    self.layer.cornerRadius = cornerRadius;
}

- (void)setBorderColor:(UIColor *)borderColor {
    self.layer.borderColor = borderColor.CGColor;
}

- (void)setBorderWidth:(CGFloat)borderWidth {
    self.layer.borderWidth = borderWidth;
}

@end

```

So now every UIView in the application can have the <code>cornerRadius</code>, <code>borderColor</code> and <code>borderWidth</code> elements styled:

``` javascript
{
	"UIView": {
		"cornerRadius": 3,
		"borderColor": [26, 26, 26],
		"borderWidth": 1
	}
}
```

Custom UIAppearance methods enable more elements to be exposed for styling. [UISS](http://github.com/robertwijas/UISS) makes this code more readable by allowing styles to be defined using JSON.