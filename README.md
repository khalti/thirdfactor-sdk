# ThirdFactor Flutter Library

The `thirdfactor` library provides Flutter widgets for initializing the ThirdFactor Verification process in a Flutter application. It includes components for onboarding, web view, and client configurations.

## Installation

Add the following dependency to your `pubspec.yaml` file:

```yaml
dependencies:
  thirdfactor: ^<latest_version>
```

Run the following command to install the package:

```bash
flutter pub get
```

## Installation

### 1. Import the Library

```dart
import 'package:thirdfactor/thirdfactor.dart';
```

### 2. Initialize ThirdFactorScope

Wrap your application with ThirdFactorScope to enable ThirdFactor Verification:

```dart
void main() {
  runApp(
    ThirdFactorScope(
      clientId: "YOUR_CLIENT_ID",
      builder: (context, navigatorKey) {
        return MaterialApp(
          navigatorKey: navigatorKey,
          // Your app configuration here
        );
      },
    ),
  );
}
```

#### Customizing Transition Animation

You can customize the transition animation when pushing or popping routes using the transitionBuilder parameter in ThirdFactorScope.

```dart
ThirdFactorScope(
  // Other parameters ....
  transitionBuilder: (_, animation, __, child) {
    const begin = Offset(1.0, 0.0);
    const end = Offset.zero;
    const curve = Curves.easeInOut;
    var tween = Tween(begin: begin, end: end).chain(CurveTween(curve: curve));
    var offsetAnimation = animation.drive(tween);
    return SlideTransition(
      position: offsetAnimation,
      child: child,
    );
  },
  transitionDuration: Duration(millisecond:300),
  reverseTransitionDuration: Duration(millisecond:300),
)
```

#### Customizing Loading Indicator

You can customize the loading animation when initializing the ThirdFactor Verifcation.

```dart
ThirdFactorScope(
  // Other parameters ....
  loadingBuilder: (progress) {
    return YourCustomLoadingWidget();
  },
)
```

### 3. Start Verification

Initiate the ThirdFactor Verification process in your app:

```dart
void startVerification() async {
  // Get verification URL from your server
  String verificationUrl = await yourServerApi.getVerificationUrl();

  // Start ThirdFactor Verification
  await ThirdFactorScope.of(context).startVerification(
    verificationUrl: verificationUrl,
    onCompletion: (response) {
      // Handle the verification completion response
      print("Verification Status: ${response.status}");
      print("Verification Message: ${response.message}");

      if (response.imageBytes != null) {
        print("Image URL: ${response.imageBytes}");
      }
    },
  );
}

```

For more advanced configurations, you can customize the onboarding process using `TfOnboardingOptions`.

```dart
TfOnboardingOptions onboardingOptions = TfOnboardingOptions(
  onboardingPages: [
    // Your onboarding pages/widgets here
    OnboardingPageWidget("Page 1", "Description 1", Icons.accessibility),
    OnboardingPageWidget("Page 2", "Description 2", Icons.book),
    OnboardingPageWidget("Page 3", "Description 3", Icons.camera),
  ],
  onDone: () {
    // Callback when Done button is pressed
    print("Onboarding Completed");
  },
  onPageChanged: (index) {
    // Callback when page changes
    print("Page changed to $index");
  },
  showSkip: false,
  showNext: true,
  dotsDecorator: TfDotsDecorator(), // Dots decorator customization
  animationDuration: 500,
  curve: Curves.easeInOut,
  controlsPadding: EdgeInsets.all(32.0),
  scrollPhysics: BouncingScrollPhysics(),
  controlAlignment: Alignment.topCenter, // Alignment for control buttons
);
```
