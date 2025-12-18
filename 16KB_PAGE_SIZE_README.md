# 16 KB Page Size Compatibility

## Issue
This is a local copy of the body_detection package modified to address Google Play's 16 KB page size requirement.

## Problem
The Google ML Kit libraries used by this package include native libraries (`libxeno_native.so`) that don't currently support devices with 16 KB memory page sizes. This affects:
- `com.google.mlkit:pose-detection-accurate:18.0.0-beta5`
- `com.google.mlkit:segmentation-selfie:16.0.0-beta6`

Specifically, the `libxeno_native.so` library (MLKitXenoCommon) doesn't support 16 KB pages.

## Current Solution
We've added packaging configuration in `android/build.gradle` to use legacy packaging for JNI libraries:

```gradle
packaging {
    jniLibs {
        useLegacyPackaging = true
    }
}
```

And specified supported ABIs:
```gradle
ndk {
    abiFilters 'armeabi-v7a', 'arm64-v8a', 'x86_64'
}
```

## Limitations
**This configuration does NOT fully solve the 16 KB page size issue.** It only helps with packaging, but the underlying native libraries still don't support 16 KB pages.

## Options

### Option 1: Wait for Google Update (Recommended)
Google is aware of this issue and working on updates to ML Kit. Track progress at:
- https://issuetracker.google.com/issues/348951515

### Option 2: Request Extension from Google Play
Google Play Console provides extensions for apps that depend on third-party libraries (like ML Kit) that haven't been updated yet.

### Option 3: Alternative Body Detection Solutions
Consider switching to alternative body detection libraries that don't depend on Google ML Kit:
- TensorFlow Lite with custom models
- MediaPipe (but also from Google, may have same issues)
- Build custom solution using OpenCV

## Checking for Updates
Periodically check for updated ML Kit versions:
```bash
# Check Maven Central for latest versions
# https://maven.google.com/web/index.html?q=pose-detection-accurate
# https://maven.google.com/web/index.html?q=segmentation-selfie
```

## Temporary Workaround for Testing
For testing on devices with 16 KB pages during development, you can temporarily add this to your `gradle.properties`:
```properties
android.bundle.enableUncompressedNativeLibs=false
```
**Note:** This property is deprecated and should not be used for production releases.

## Last Updated
December 2025

## References
- [Google Issue Tracker - 16KB page size support](https://issuetracker.google.com/issues/348951515)
- [Android 16 KB page size requirement](https://developer.android.com/guide/practices/page-sizes)
