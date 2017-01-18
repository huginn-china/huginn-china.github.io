---
title: ASDisplayNode
layout: docs
permalink: /docs/display-node.html
prevPage: containers-aspagernode.html
nextPage: cell-node.html
---

### Node Basics

<code>ASDisplayNode</code> is the main view abstraction over <code>UIView</code> and <code>CALayer</code>.  It initializes and owns a <code>UIView</code> in the same way <code>UIViews</code> create and own their own backing <code>CALayers</code>.

<div class = "highlight-group">
<span class="language-toggle"><a data-lang="swift" class="swiftButton">Swift</a><a data-lang="objective-c" class = "active objcButton">Objective-C</a></span>

<div class = "code">
	<pre lang="objc" class="objcCode">
ASDisplayNode *node = [[ASDisplayNode alloc] init];
node.backgroundColor = [UIColor orangeColor];
node.bounds = CGRectMake(0, 0, 100, 100);

NSLog(@"Underlying view: %@", node.view);
	</pre>

	<pre lang="swift" class = "swiftCode hidden">
let node = ASDisplayNode()
node.backgroundColor = UIColor.orangeColor()
node.bounds = CGRect(x: 0, y: 0, width: 100, height: 100)

print("Underlying view: \(node.view)")
	</pre>
</div>
</div>

A node has all the same properties as a <code>UIView</code>, so using them should feel very familiar to anyone familiar with UIKit.

Properties of both views and layers are forwarded to nodes and can be easily accessed.

<div class = "highlight-group">
<span class="language-toggle"><a data-lang="swift" class="swiftButton">Swift</a><a data-lang="objective-c" class = "active objcButton">Objective-C</a></span>

<div class = "code">
	<pre lang="objc" class="objcCode">
ASDisplayNode *node = [[ASDisplayNode alloc] init];
node.clipsToBounds = YES;				  // not .masksToBounds
node.borderColor = [UIColor blueColor];  //layer name when there is no UIView equivalent

NSLog(@"Backing layer: %@", node.layer);
	</pre>

	<pre lang="swift" class = "swiftCode hidden">
let node = ASDisplayNode()
node.clipsToBounds = true			     // not .masksToBounds
node.borderColor = UIColor.blueColor()  //layer name when there is no UIView equivalent

print("Backing layer: \(node.layer)")
	</pre>
</div>
</div>

As you can see, naming defaults to the <code>UIView</code> conventions<a href = "/docs/display-node.html#addendum">***</a> unless there is no <code>UIView</code> equivalent.  You also have access to your underlying <code>CALayer</code> just as you would when dealing with a plain <code>UIView</code>.

When used with one of the <a href = "/docs/getting-started.html#node-containers">node containers</a>, a node’s properties will be set on a background thread, and its backing view/layer will be lazily constructed with the cached properties collected by the node.  You rarely need to worry about jumping to a background thread as this will be taken care of by the framework, but it's important to know that this is happening under the hood.

### View Wrapping

In some cases, it is desirable to initialize a node and provide a view to be used as the backing view.  These views are provided via a block that will return a view so that the actual construction of the view can be saved until later.  These nodes’ display step happens synchronously.  This is because a node can only be asynchronously displayed when it wraps an <code>_ASDisplayView</code> (the internal view subclass), not when it wraps a plain <code>UIView</code>.

<div class = "highlight-group">
<span class="language-toggle"><a data-lang="swift" class="swiftButton">Swift</a><a data-lang="objective-c" class = "active objcButton">Objective-C</a></span>

<div class = "code">
	<pre lang="objc" class="objcCode">
ASDisplayNode *node = [ASDisplayNode alloc] initWithViewBlock:^{
	SomeView *view  = [[SomeView alloc] init];
	return view;
}];
	</pre>

	<pre lang="swift" class = "swiftCode hidden">
let node = ASDisplayNode(viewBlock: { () -> UIView! in
    let view = SomeView();
    return view
})
	</pre>
</div>
</div>

Doing this allows you to wrap existing views if that is preferable to converting the <code>UIView</code> subclass to an <code>ASDisplayNode</code> subclass.

<div class = "note" id = "addendum">
	<a href = "/docs/display-node.html#addendum">***</a> The only exception is that nodes use <code>position</code> instead of <code>center</code> for reasons beyond this intro.
</div>
