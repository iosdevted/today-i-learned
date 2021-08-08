# Frame & Bounds

All `UIView` subclasses have two properties that at first glance seem similar: `frame` and `bounds`. Both return a `CGRect` – a rectangle containing their X and Y position, plus their width and height – but that doesn’t mean they are the same.

<img src="https://user-images.githubusercontent.com/50784573/110612294-63f4c400-81d3-11eb-9d95-75b0ff5873ec.png" height=400/>

At its simplest, `a view’s bounds refers to its coordinates relative to its own space (as if the rest of your view hierarchy didn’t exist)`, whereas its `frame refers to its coordinates relative to its parent’s space.`

## frame

The frame of an UIView is the rectangle, expressed as a location (x,y) and size (width,height) relative to the superview it is contained within.

<img src="https://user-images.githubusercontent.com/50784573/110607770-e4fd8c80-81ce-11eb-9811-77464c741829.png" height=400/>
<img src="https://user-images.githubusercontent.com/50784573/110612072-2ee87180-81d3-11eb-85f2-759e2f82abb2.png" height=400/>

We use the frame

- When setting the UIView position or size.

## bounds

It has nothing to do with the superview, it has the standard.
Bounds are rectangles that are expressed in position (x, y) and size (width, height) based on their own coordinate system (0,0). So the origin of Bounds is (0,0) by default.

<img src="https://user-images.githubusercontent.com/50784573/110607765-e333c900-81ce-11eb-9562-f4661641a2bc.png" height=400/>

We use the bounds

- When drawing inside the View (drawRect).
- When you want to know the size of the view after transfomation.
- When changing internally, such as sorting subviews.

## References

[iOS) Frame vs Bounds (1/2)](https://zeddios.tistory.com/203)

[iOS) Frame vs Bounds (2/2)](https://zeddios.tistory.com/231)