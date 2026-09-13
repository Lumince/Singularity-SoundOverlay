# StartupSoundOverlay

A minimal [Android Runtime Resource Overlay (RRO)](https://source.android.com/docs/core/runtime/rros) that replaces the Meta Quest Headsets's
boot chime (`res/raw/startup_sound.wav` inside `framework-res.apk`).

## Requirements

- Rooted Meta Quest Headset (Pre-August 4th 2026 firmware)
- Magisk installed (Either with [Singularity](https://github.com/Lumince/singularity/releases) or by other means)
- Android Studio / Gradle to build the APK.

## Building

1. Drop your desired sound file in as
   `app/src/main/res/raw/startup_sound.wav`.
2. Android Studio → Generate Singed Apk → debug variant
3. Output lands at `app/debug/StartupSoundOverlay.apk`

## Testing (no hard reboot required)

This is just copying `/product/overlay`, adding the RRO apk we built eariler to it, making sure the permissions are correct for selinux, mount binding over `/product/overlay`, and soft rebooting

Be careful with soft rebooting a lot. This can cause instability/crashes if done too many times.


```
adb push StartupSoundOverlay.apk /data/local/tmp/StartupSoundOverlay.apk
adb shell su -c "mkdir -p /data/adb/sound_overlay"
adb shell su -c "umount -l /product/overlay"
adb shell su -c "cp -a /product/overlay/. /data/adb/sound_overlay/"
adb shell su -c "cp /data/local/tmp/StartupSoundOverlay.apk /data/adb/sound_overlay/StartupSoundOverlay.apk"
adb shell su -c "magisk --clone-attr /product/overlay /data/adb/sound_overlay"
adb shell su -c "mount -o bind /data/adb/sound_overlay /product/overlay"
adb shell su -c "ls -laZ /product/overlay"
adb shell su -c "killall zygote64"
```
