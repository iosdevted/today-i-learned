# [HowTo] Custom Font

To use a custom font, you must follow Apple's standards. Also, keep in mind that custom fonts cannot be used on the launching screen.

### 1. Download the font

At this time, you will be asked whether to receive the TTF for Windows or the OTF for Mac. However, you can choose any of the two.

### 2. Add the downloaded font to the project

### 3. Check Target Membership

Click on the font added above, and make sure to check Target Membership in the inspector on the right.

<div>
    <img src="https://user-images.githubusercontent.com/50784573/112622791-e08fcf80-8e6e-11eb-8716-1894ede3123f.png" width=780/>
</div>

### 4. Check if the font is well added to the resource

When adding a font, if you checked Copy if you needed, it would be added.

<div>
    <img src="https://user-images.githubusercontent.com/50784573/112623266-7d526d00-8e6f-11eb-8213-0dc74118df05.png" width=780/>
</div>

### 5. Add font to info.plist

Add Fonts provided by application, and put the name of the font in the value of the item below it.

<div>
    <img src="https://user-images.githubusercontent.com/50784573/112623776-1d0ffb00-8e70-11eb-9630-12fb12af8bbd.png" width=780/>
</div>

