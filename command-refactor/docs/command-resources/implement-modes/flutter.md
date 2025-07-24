# Flutter Implementation Mode

This guide provides Flutter-specific patterns and best practices for implementing tasks in the Scopecraft ecosystem.

## Flutter Development Mindset

As a Flutter developer, you focus on:
- Cross-platform mobile/desktop application development
- Dart programming language patterns
- Widget-based architecture
- State management solutions
- Platform-specific integrations
- Performance optimization for mobile devices

## Core Flutter Patterns

### 1. Widget Structure
```dart
// Stateless widgets for UI that doesn't change
class MyWidget extends StatelessWidget {
  const MyWidget({super.key, required this.data});
  
  final Data data;
  
  @override
  Widget build(BuildContext context) {
    return Container(
      child: Text(data.title),
    );
  }
}

// Stateful widgets for interactive UI
class MyStatefulWidget extends StatefulWidget {
  const MyStatefulWidget({super.key});
  
  @override
  State<MyStatefulWidget> createState() => _MyStatefulWidgetState();
}

class _MyStatefulWidgetState extends State<MyStatefulWidget> {
  // State variables here
  
  @override
  Widget build(BuildContext context) {
    return Container();
  }
}
```

### 2. State Management
Follow established patterns:
- **Provider/Riverpod** for dependency injection and state
- **BLoC** for complex business logic
- **setState** for simple local state
- **ValueNotifier/ChangeNotifier** for observable state

### 3. Directory Structure
```
lib/
├── main.dart
├── app/
│   ├── app.dart
│   └── router.dart
├── features/
│   └── {feature_name}/
│       ├── data/
│       ├── domain/
│       ├── presentation/
│       └── {feature_name}.dart
├── core/
│   ├── constants/
│   ├── utils/
│   ├── widgets/
│   └── services/
└── shared/
    ├── models/
    ├── widgets/
    └── utils/
```

## Implementation Guidelines

### 1. Code Quality
- Follow Dart style guide and conventions
- Use `dart analyze` and `dart format` regularly
- Implement proper null safety
- Use meaningful widget and variable names
- Add comprehensive documentation comments

### 2. Performance Best Practices
- Use `const` constructors whenever possible
- Implement proper widget disposal in StatefulWidgets
- Optimize build methods (avoid heavy computations)
- Use ListView.builder for long lists
- Implement proper image caching and optimization

### 3. Testing Approach
```dart
// Widget tests
testWidgets('MyWidget displays correct data', (WidgetTester tester) async {
  await tester.pumpWidget(MyApp());
  expect(find.text('Expected Text'), findsOneWidget);
});

// Unit tests
test('Business logic test', () {
  final result = businessLogic.process(input);
  expect(result, expectedOutput);
});

// Integration tests
testWidgets('Full app flow test', (WidgetTester tester) async {
  // Test complete user workflows
});
```

### 4. Error Handling
```dart
// Proper error handling
try {
  final result = await riskyOperation();
  return Right(result);
} catch (e) {
  debugPrint('Error in operation: $e');
  return Left(AppError.fromException(e));
}

// User-friendly error display
Widget buildErrorWidget(String error) {
  return Column(
    children: [
      Icon(Icons.error_outline),
      Text('Something went wrong'),
      Text(error, style: Theme.of(context).textTheme.bodySmall),
    ],
  );
}
```

## Flutter-Specific Considerations

### 1. Platform Integration
- Use platform channels for native functionality
- Implement proper iOS/Android specific UI patterns
- Handle platform permissions correctly
- Consider platform-specific design guidelines

### 2. Asset Management
- Organize assets in logical folders
- Use proper image resolution variants
- Implement efficient font loading
- Consider app size optimization

### 3. Navigation
```dart
// Use proper routing
GoRouter(
  routes: [
    GoRoute(
      path: '/home',
      builder: (context, state) => HomeScreen(),
    ),
  ],
);

// Or Navigator 2.0 patterns
Navigator.of(context).pushNamed('/screen');
```

## Quality Checklist

Before marking Flutter implementation complete:

### Code Quality
- [ ] Dart analyzer passes with no warnings
- [ ] Code is properly formatted (`dart format`)
- [ ] All widgets use const constructors where possible
- [ ] Proper null safety implemented
- [ ] No unused imports or variables

### Performance
- [ ] Build methods are optimized
- [ ] Proper widget disposal implemented
- [ ] Images are optimized and cached
- [ ] List rendering uses efficient patterns
- [ ] Memory leaks checked and prevented

### Testing
- [ ] Widget tests cover UI components
- [ ] Unit tests cover business logic
- [ ] Integration tests cover user flows
- [ ] Edge cases and error scenarios tested
- [ ] Platform-specific functionality tested

### Documentation
- [ ] Complex widgets documented
- [ ] API integrations documented
- [ ] State management patterns documented
- [ ] Performance considerations noted

## Common Flutter Pitfalls to Avoid

- ❌ Building widgets in build methods unnecessarily
- ❌ Not disposing controllers and streams
- ❌ Ignoring platform-specific design patterns
- ❌ Overusing setState for complex state
- ❌ Not handling async operations properly
- ❌ Forgetting to test on both iOS and Android
- ❌ Not considering different screen sizes
- ❌ Blocking the UI thread with heavy computations

## Integration with Scopecraft

### 1. Task Management Integration
- Connect Flutter app to Scopecraft MCP tools
- Implement task display and management widgets
- Handle real-time updates for task status

### 2. Cross-Platform Considerations
- Ensure Flutter app works on target platforms
- Consider desktop vs mobile UI patterns
- Implement proper responsive design

### 3. State Synchronization
- Sync app state with Scopecraft backend
- Handle offline/online state management
- Implement proper data persistence

## Tools and Commands

```bash
# Flutter development commands
flutter doctor                    # Check Flutter installation
flutter create my_app            # Create new Flutter project
flutter run                      # Run app in debug mode
flutter build apk               # Build Android APK
flutter build ios               # Build iOS app
flutter test                    # Run all tests
flutter analyze                 # Run static analysis
dart format .                   # Format code
```

## Resources

- [Flutter Documentation](https://docs.flutter.dev/)
- [Dart Language Tour](https://dart.dev/guides/language/language-tour)
- [Flutter Widget Catalog](https://docs.flutter.dev/development/ui/widgets)
- [Material Design Components](https://docs.flutter.dev/development/ui/material)
- [Cupertino Design Components](https://docs.flutter.dev/development/ui/cupertino)