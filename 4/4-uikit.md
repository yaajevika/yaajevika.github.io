# UIKit Basics

UIKit is Apple’s mature framework for building user interfaces on iOS, iPadOS, and tvOS.

## Core Concepts of UIKit

This is the key classes responsible for the UI in UIKit:

- `UIView`: The base class for all visual elements
- `UIViewController`: Controls a screen of content
- `UIWindow`: The container for all views in your app

In order to place the views, you can use these layout systems:

- **Frame-based layout**: Setting explicit coordinates and sizes
- **Auto Layout**: Constraint-based adaptive layouts
- `UIStackView`: Simplified layouts for linear content

## Creating Views

### Programmatic Approach

```swift
let button = UIButton(type: .system)button.setTitle("Tap Me", for: .normal)button.backgroundColor = .systemBlue
button.layer.cornerRadius = 8button.addTarget(self, action: #selector(buttonTapped), for: .touchUpInside)view.addSubview(button)
```

### Interface Builder

- Use Storyboards for visual design and navigation flow
- Use XIB files for reusable view components
- Connect UI elements using outlets and actions

### Combining Approaches

- Create base layouts in Interface Builder
- Add dynamic elements programmatically

## Auto Layout and Constraints

Auto Layout is a constraint-based layout system for UIKit that allows you to create adaptive interfaces that respond properly to different screen sizes, device orientations, and dynamic content.

Constraints are mathematical relationship objects that define how views should be positioned and sized relative to other elements. Each constraint represents an equation:

```swift
item1.attribute1 = multiplier * item2.attribute2 + constant
```

For example, a constraint might specify “button’s leading edge = superview’s leading edge + 20 points”.

Many UI elements (like labels and buttons) have a natural size based on their content. Auto Layout respects this intrinsic content size when calculating layouts.

### Creating Constraints

You can create constraint using Interface Builder or programmatically.

Using Interface Builder:

1. Add views to your storyboard of XIB
2. Control-drag between views to create constraints
3. Use the Pin menu (square icon with lines) to set specific contsraint
4. Use the Align menu to align views
5. Resolve layout issues with the issues navigator

Programmatically, `NSLayoutConstraint`’s static method `activate`:

```swift
let button = UIButton(type: .system)
button.translatesAutoresizingMaskIntoConstraints = false
view.addSubview(button)

NSLayoutConstraint.activate([
	button.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 16),
	button.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 16),
	button.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -16),
	button.heightAnchor.constraint(equalToConstant: 32)
])
```

Using Visual Format Language:

```swift
let views = ["button": button]
let metrics = ["padding": 16, "buttonHeight": 32]

NSLayoutConstraint.activate(
	NSLayoutConstraint.constraints(
		withVisualFormat: "H:|-padding-[button]-padding-|",
		options: [],
		metrics: metrics,
		views: views) +
	NSLayoutConstraint.constraints(
		withVisualFormat: "V:|-padding-[button(==buttonHeight)]|",
		options: [],
		metrics: metrics,
		views: views)
)
```

Key attributes for constraints:

- Position: `leadingAnchor`, `trailingAnchor`, `topAnchor`, `bottomAnchor`, `centerXAnchor`, `centerYAnchor`
- Size: `widthAnchor`, `heightAnchor`
- Margins: `layoutMarginGuide`
- Safe Area: `safeAreaLayoutGuide`

Constraint have priorities from 1 to 1000:

- **Required (1000)**: Must be satisfied
- **High (750)**: Should be satisfied if possible
- **Low (250)**: Optional

```swift
let constraint = button.widhtAnchor.constraint(equalToConstant: 200)
constraint.priority = .defaultHigh
constraint.isActive = true
```

Layout Guides can help with the element’s placement:

- `UILayoutGuide`: A non-visual element that participates in Auto Layout
- `safeAreaLayoutGuide`: Defines areas not covered by navigation bars, tab bars, etc.
- `layoutMarginsGuide`: Defines the interior margin of a view

Sometimes, layout issues might happen. For example, ambiguous layouts might be creates or some constraints might conflict with another.

Ambiguous layout is a situation when Auto Layout doesn’t have enough constraints to determine a proper and unique solution:

```swift
// Check if a view has ambiguous layout
print(view.hasAmbiguousLayout)

// Visualize constraint issues
view.exerciseAmbiguityInLayout()
```

Conflicting constraints is a situation when constraints contradict each other, some of those constraints must be broken to find a solution.

```swift
// Break in debugger on constraint conflicts
UIView.setShowLargeContentView(true)
```

Visual format debugging:

```swift
print(view.constraints)
print(view.constraintsAffectingLayout(for: .horizontal))
```

### Auto Layout best practices:

1. Set `translatesAutoresizingMaskIntoConstraints` to `false` for views managed by Auto Layout
2. Use constraint activation rather than adding constraints to views directly
3. Avoid hardcoded values for multiplatform apps
4. Use `UIStackView` for simpler layouts
5. Consider intrinsic content size when designing your layout
6. Use compression and hugging priorities to control how views resize
7. Use layout guides for spacing and organisation
8. Test on multiple device sizes to ensure your layout adapts properly

## UIStackView

`UIStackView` is a powerful layout container that simplifies the creation of common interface designs by automatically managing constraints for its contained views. This container arranges its subviews (called “arranged views”) in either a horizontal or vertical line. Rather than manually creating complex sets of constraints, you define high-level attributes that determine how the stack organises its contents.

Key properties of `UIStackView`:

1. **axis**: Determines whether views are stacked horizontally (`.horizontal`) or vertically (`.vertical`)
2. **distribution**: Controls how arranged views are sized along the stack’s axis
    - `.fill`: Default, fills available space based on intrinsic content size
    - `.fillEqually`: Equal sizes for all arranged views
    - `.fillProportionally`: Sizes views proportionally to their intrinsic content sizes
    - `.equalSpacing`: Equal spacing between views
    - `.equalCentering`: Equal distances between view centers
3. **alignment**: Controls how views are positioned perpendicular to the stack’s axis
    - `.fill`: Stretches views to fill the stack’s width/height
    - `.leading`/`.trailing`: Aligns to the leading/trailing edge
    - `.center`: Centers views
    - `.firstBaseline`/`.lastBaseline`: Aligns text baselines (useful for text elements)
4. **spacing**: Defines the minimum spacing between adjacent views
5. **isLayoutMarginsRelativeArrangement**: When `true`, content is arranged relative to the layout margins

`UIStackView` automatically creates, manages, and updates the Auto Layout constraints needed to implement its layout design. This happens behind the scenes and provides several advantages.

1. **Simplified code**: No need to create and activate numerous constraints manually
2. **Dynamic adaptation**: Automatically updates constraints when content or container changes
3. **Reduced conflicts**: Lower risk of creating conflicting constraints

UIStackView can be created and filled with arranged views either programatically or in Interface Builder.

```swift
let playButton = UIButton(type: .system)
playButton.setTitle("Play", for: .normal)

let pauseButton = UIButton(type: .system)
pauseButton.setTitle("Pause", for: .normal)

let controlStack = UIStackView(arrangedSubviews: [playButton, pauseButton])
controlStack.axis = .horizontal
controlStack.alignment = .center
controlStack.distribution = .equalSpacing
controlStack.spacing = 12
...
// Placing the stack view into its superview and adding constraints to it
```

One of the most powerful features of `UIStackView` is the ability to nest them for complex layouts:

```swift
let mainStackView = UIStackView()
mainStackView.axis = .vertical
mainStackView.spacing = 16
mainStackView.alignment = .fill
mainStackView.translatesAutoresizingMaskIntoConstraints = false

mainStackView.addArrangedSubview(row1StackView)
mainStackView.addArrangedSubview(row2StackView)
mainStackView.addArrangedSubview(row3StackView)
```

`UIStackView` makes it easy to add, remove, or rearrange views:

```swift
// Add a view
stackView.addArrangedSubview(newView)

// Insert a view at a specific index
stackView.insertArrangedSubview(newView, at: 2)

// Remove a view
stackView.removeArrangedSubview(viewToRemove)
viewToRemove.removeFromSuperview() // needed to fully remove the view

// Hide a view (keeps it in the arrangement but with zero size)
arrangedView.isHidden = true
```

### UIStackView and Constraints: Best Practices

1. **Set constraints on the stack view itself**, not on its arranged subviews
2. **Use `.fill` distribution with size constraints** on individual views
3. **Set content hugging and compression resistance priorities** on arranged views to control resizing behaviour
4. **Combine multiple stack views** rather than creating complex single stack views
5. **Use background views** when you need backgrounds or borders around grouped elements

## Lifecycle of UIController vs Lifecycle of app

Understanding the lifecycle methods in UIKit is crucial for properly managing resources, responding to state changes, and creating a smooth user experience.

### UIViewController Lifecycle

**`init(coder:`)/`init(nibName:bundle:)`**

Called during view controller’s instantiation from a storyboard or programmatically. Best used for initialising properties that don’t depend on view hierarchy.

**`loadView()`**

Called when the view controller’s view property is accessed and is currently `nil`. Best used for creating views programmatically instead of loading from a storyboard/nib.

**`viewDidLoad()`**

Called after the view is loaded into memory (once per view controller’s lifecycle). Best used for:

- one-time setup that doesn’t depend on view’s geometry
- initial data fetching
- setting up delegates and data sources

**`viewWillAppear(_:)`**

Called every time before the view becomes visible. Best used for:

- UI updates before view appears
- starting animations or timers
- reloading dynamic data

**`viewWillLayoutSubviews()`**

Called when the view’s bounds change and before subviews are laid out. Best used for making layout changes before Auto Layout runs.

**`viewDidLayoutSubviews()`**

Called after Auto Layout has updated all view’s frames. Best used for:

- layout adjustments that need final view frames
- custom drawing that depends on layout

**`viewDidAppear(_:)`**

Called after the view is visible on screen Best used for:

- starting processes that should only run while view is visible
- triggering animations after view appears
- analytics tracking

**`viewWillDisappear(_:)`**

Called before the view is removed from the view hierarchy. Best used for:

- saving user input
- stopping ongoing processes
- preparing for view dismissal

**`viewDidDisappear(_:)`**

Called after the view is removed from the view hierarchy. Best used for:

- cleaning up resources
- stopping times, listeners, observers
- final cleanup

**`deinit`**

Called when view controller is deallocated from memory. Best used for final cleanup of external resources.

### App’s Lifecycle (UIApplicationDelegate’s methods)

**`application(_:didFinishLaunchingWithOptions:)`**

Called when app’s launch completes. Best used for:

- app-wide setup
- service initialisation
- authentication checking

**`applicationWillResignActive(_:)`**

Called when app is about the move from active to inactive state. Best used for:

- pausing ongoing tasks
- saving critical data

**`applicationDidEnterBackground(_:)`**

Called when app enters background state. Best used for:

- releasing shared resources
- saving user data
- preparing for possible termination

**`applicationWillEnterForeground(_:)`**

Called when app is about to enter foreground (but not yet active). Best used for:

- undoing background state changes
- preparing UI for becoming visible

**`applicationDidBecomeActive(_:)`**

Called when app transitions to active state. Best used for:

- restarting paused tasks
- refreshing UI

**`applicationWillTerminate(_:)`**

Called before app termination (not guaranteed to be called). Best used for final cleanup when app closes.

### Scene Lifecycle Methods (UISceneDelegate, iOS13+)

Modern iOS apps often use the scene-based lifecycle for multi-window support.

**`scene(_:willConnectTo:options)`**

Called when a new scene is being added to the app. Similar to `application(_:didFinishLaunchingWithOptions:)` but per-scene.

**`sceneWillEnterForeground(_:)`**

Called before scene moves to foreground. Similar to `applicationWillEnterForeground(_:)` **.**

**`sceneDidBecomeActive(_:)`**

Called when scene becomes active. Similar to `applicationDidBecomeActive(_:)` .

**`sceneWillResignActive(_:)`**

Called before scene becomes inactive. Similar to `applicationWillResignActive(_:)` .

**`sceneDidEnterBackground(_:)`**

Called after scene enters background. Similar to `applicationDidEnterBackground(_:)` **.**

Understanding these lifecycle methods and using them appropriately ensures your app behaves correctly during transitions, maintains good performance, and provides a seamless user experience.
