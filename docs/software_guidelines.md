
## **Software Contributing Guidelines**

1. **Use `main` instead of `master`** as your primary working branch.
2. **Follow the [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html#Naming).** Below is the quick-reference sheet for our naming conventions:

| Element | Convention | Example |
|---------|------------|---------|
| **Files** | `snake_case.cc` | `flight_sensor.cc` |
| **Variables** | `snake_case` | `packet_size` |
| **Constants** | `kPascalCase` | `kMaxRetries` |
| **Functions** | `PascalCase()` | `CalculateTrajectory()` |
| **Types / Classes** | `PascalCase` | `EngineController` |
| **Private Class Members** | `snake_case_` | `baud_rate_` |