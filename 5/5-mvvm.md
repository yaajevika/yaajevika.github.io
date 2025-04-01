# MVVM & Observable

## MVVM vs Other iOS Architecture Patterns

MVVM (Model-View-ViewModel) is one of several architectural patterns used in iOS development. It has several advantages related to other patterns. Let’s take a look at it and compare with other patterns that can be used in iOS app development.

Core components of MVVM:

- **Model**: Represents data and business logic
- **View**: The UI elements (UIViewController, UIView, View, etc.)
- **ViewModel**: Transforms model data for the view and handles view logic

Key advantages of MVVM:

- Clear separation of concerns
- Highly testable (ViewModels can be tested without UI dependencies)
- Data binding capabilities (especially with frameworks like Combine, RxSwift, etc.)
- Better state management

Let’s take a look at other architectural patterns commonly used in iOS development and compare them with MVVM.

### MVC (Model-View-Controller)

Core components of MVC:

- **Model**: Data and business logic
- **View**: UI elements
- **Controller**: Mediates between Model and View

Comparison to MVVM:

- Apple’s traditional recommended pattern for the apps based on UIKit
- Often leads to “Massive View Controllers” as controllers accumulate to much responsibility
- Less tastable than MVVM
- Simpler to implement for small applications

### MVP (Model-View-Presenter)

Core components of MVP:

- **Model**: Data and business logic
- **View**: Passive UI elements (includes UIViewController)
- **Presenter**: Handles view logic and updates the view

Comparison to MVVM:

- Similar to MVVM but with direct references to the view
- Presenter typically has a 1to1 relationship with the view
- Less data binding capability than MVVM
- Often requires more boilerplate code for view updates

### VIPER (View-Interactor-Presenter-Entity-Router)

Core components:

- **View**: UI elements
- **Interactor**: Business logic
- **Presenter**: View logic, mediates between View and Interactor
- **Entity**: Data models
- **Router**: Navigation logic

Comparison to MVVM:

- Much more granular separation of concerns
- Better for large, complex applications
- Higher learning curve and much more boilerplate code
- Excellent for testability and maintainability
- Better separation of navigation logic

### VIP (View-Interactor-Presenter)

Core components:

- View: UI elements
- Interactor: Contains business logic
- Presenter: Formats data for display

Comparison to MVVM:

- Follows an unidirectional data flow
- Stronger emphasis on use cases/business logic
- Often more complex to implement
- Better for enterprise-level applications

MVVM is a great choice when:

- Your app has complex UI logic
- You need hight test coverage
- You are using reactive programming (Combine, RxSwift)
- You want a balance between separation of concerns and implementation complexity

## MVVM with Observable

SwiftUI and MVVM form a natural pairing, with SwiftUI’s declarative approach complementing MVVM’s separation of concerns. The `@Observable` macro, introduced in iOS 17, has further streamlined this relationship.

Let’s take a look at MVVM implementation in a typical SwiftUI app:

- Model: Data structures and business logic, typically plain Swift types
- View: SwiftUI views that declare UI elements based on state from the ViewModel
- ViewModel: The connector between Model and View, providing:
    - transformed data from the Model in a format ready for display
    - methods that handle user interactions
    - published state that the View observes

The `@Observable` macro provides a streamlined way to implement the ViewModel in MVVM:

```swift
@Observable class ProfileViewModel {
	var username = ""
	var bio = ""
	var isValid: Bool {
		!username.isEmpty && bio.count <= 160
	}

	func fetchProfile() {
		// Call repository/service to get data
		// Update properties when data arrives
	}

	func saveProfile() {
		// Business logic to validate and save
	}
}
```

What happens here:

- **Automatic property observation**: The `@Observable` macro automatically makes the ViewModel properties observable by SwiftUI
- **View reactivity**: SwiftUI efficiently updates only the parts of the view that depend on changed properties
- **Dependency tracking**: SwiftUI tracks which view elements depend on which ViewModel properties

This is how the MVVM components might be implemented in our example.

### Model:

```swift
struct User {
	let id: UUID
	var name: String
	var email: String
	var bio: String
}
```

### ViewModel:

```swift
@Observable class UserProfileViewModel {
	private var user: User?

	var name = ""
	var email = ""
	var bio = ""
	var isLoading = false
	var errorMessage = ""

	func loadUser(id: UUID) {
		isLoading = true

		// Simulated network call
		DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
			self.user = User (id: id,
												name: "John Doe",
												email: "john@example.com",
												bio: "iOS Developer")
			self.name = self.user?.name ?? ""
			self.email = self.user?.email ?? ""
			self.bio = self.user?.bio ?? ""
			self.isLoading = false
		}
	}

	func saveUser() {
		guard let userId = user?.id else { return }

		// Update user model
		user = User (id: userId, name: name, email: email, bio: bio)

		// Send data to backend
	}
}
```

### View

```swift
struct UserProfileView: View {
	@State privte var viewModel = UserProfileViewModel()
	let userId: UUID

	var body some View {
		Form {
			Section("Profile Details") {
				TextField("Name", text: $viewModel.name)
				TextField("Email", text: $viewModel.email)

				ZStack(alignment: .topLeading) {
					TextEditor(text: $viewModel.bio)
						.frame(height: 100)

					if viewModel.bio.isEmpty {
						Text("Bio")
							.foregroundColor(.gray.opacity(0.8))
							.padding(.top, 8)
							.padding(.leading, 4)
					}
				}
			}

			Button("Save Changes") {
				viewModel.saveUser()
			}
		}
		.overlay {
			if viewModel.isLoading {
				ProgressView()
					.background(Color.white.opacity(0.7))
			}
		}
		.onAppear {
			viewModel.loadUser(id: userId)
		}
	}
}
```

To get the same functionality without `@Observable` macro you have to use several property wrappers:

- `@ObservedObject` : For reference types that conform to `ObservableObject` protocol
- `@StateObject` : Similar to `@ObservedObject` but with different lifecycle management
- `@Published` : Used within `ObservableObject` types to mark properties that trigger view updates

The `@Observable` macro simplifies things by eliminating the need for `@Published` markers, providing more fine-grained updates, requiring less boilerplate code.

Here’s the advantages of using MVVM pattern with `@Observable`:

- **Clean separation of concerns**: UI logic stays in the View, business logic is moved to the ViewModel
- **Enhanced testability**: ViewModels can be tested independently
- **Improved code organisation**: Well-defined responsibilities for each component
- **Reactive UI updates**: Automatic view updates when ViewModel’s data changes
- **Reduced boilerplate**: `@Observable` eliminates much of the publication/subscription code

MVVM with `@Observable` represents the most modern and efficient approach to architecting SwiftUI applications, combining the structural benefits of MVVM with SwiftUI’s reactive nature.

## Homework

- Implement MVVM in the feature they’re currently working on, by adding field and button validations;
