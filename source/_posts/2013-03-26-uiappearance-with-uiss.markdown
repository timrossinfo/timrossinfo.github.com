---
layout: post
title: "Improving Readability of UIAppearance Code with UISS"
date: 2013-03-26 10:14
comments: true
categories: iOS
---
[UIAppearance](http://useyourloaf.com/blog/2012/08/24/using-appearance-proxy-to-style-apps.html) is a convenient way to define styles for UIKit classes throughout your application. For example, instead of setting the font on every UILabel, you can define it once using a UIAppearance proxy:

``` objective-c
[[UILabel appearance] setFont:[UIFont fontWithName:@"Avenir-Medium" size:11]];
```

Of course this is Objective-C, so the UIAppearance code is verbose and can be quite difficult to read. If you work with a designer who might like to tweak these values, the code can appear daunting. Luckily there's a quick and simple way to make UIAppearance code easier to read (and write!).

## Introducing UISS

[UISS](http://github.com/robertwijas/UISS) stands for UIKit Style Sheets. UISS is an iOS library build on top of UIAppearance proxies and provides a convenient way to define styles using JSON.

Let's look at a more complex example of styles defined in Objective-C using UIAppearance:

``` objective-c
[[UIButton appearance] setTitleColor:[UIColor colorWithRed:204.f/255.f green:204.f/255.f blue:204.f/255.f alpha:1] forState:UIControlStateNormal];
[[UIButton appearance] setTitleColor:[UIColor colorWithRed:160.f/255.f green:160.f/255.f blue:160.f/255.f alpha:1] forState:UIControlStateHighlighted];
[[UIButton appearance] setTitleShadowColor:[UIColor colorWithWhite:0 alpha:0.7] forState:UIControlStateNormal];
[[UIButton appearance] setBackgroundImage:[[UIImage imageNamed:@"ButtonGrey"] resizableImageWithCapInsets:UIEdgeInsetsMake(10, 10, 10, 10)] forState:UIControlStateNormal];
[[UIButton appearance] setBackgroundImage:[[UIImage imageNamed:@"ButtonGreyTap"] resizableImageWithCapInsets:UIEdgeInsetsMake(10, 10, 10, 10)] forState:UIControlStateHighlighted];
[[UILabel appearanceWhenContainedIn:[UIButton class], nil] setFont:[UIFont fontWithName:@"Avenir-Medium" size:11]];
[[UILabel appearanceWhenContainedIn:[UIButton class], nil] setShadowOffset:CGSizeMake(0, 1)];
```

Now here's the equivalent styles defined in JSON using UISS:

``` javascript
{
	"UIButton": {
		"titleColor:normal": [204, 204, 204],
		"titleColor:highlighted": [160, 160, 160],
		"titleShadowColor:normal": ["black", 0.7],
		"backgroundImage:normal": ["ButtonGrey", 10, 10, 10, 10],
		"backgroundImage:highlighted": ["ButtonGreyTap", 10, 10, 10, 10],
		"UILabel": {
			"font": ["Avenir-Medium", 11],
			"shadowOffset": [0, 1]
		}
	}
}
```

The UISS code is not only easier to read, but also more pleasurable to write!

UISS includes logging and a console for troubleshooting style problems. It can even generate the equivalent UIAppearance code from the JSON files, which is handy if you want to see exactly what's going on.

Styles can even be loaded from a remote server to enable live style updates in your app.

I was able to get up and running with UISS quickly and found it greatly improved the readability of my UIAppearance definitions. UISS is available on [GitHub](http://github.com/robertwijas/UISS) or as a [Cocoapod](http://cocoapods.org/?q=uiss).