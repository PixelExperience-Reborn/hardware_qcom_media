# Android17 PixelExperience-Reborn — legacy Qualcomm media source audit for mondrian

**2026-11-05 | P2 matrix row 152: `hardware/qcom/media` | archived OMX/Stagefright HAL — not an active Taro implementation.**

## Verified legacy code scope

- PE branch `seventeen` HEAD `cdaf50847127874dbdf58929438b1f26593b7946` contains `msm8974`, `msm8996`, `msm8998` trees. Root `Android.mk` selects OMX / libstagefrighthw / V4L2 and C2D modules only for explicitly listed older platforms; **`taro` is not in any active list**.
- Its default `QCOM_MEDIA_ROOT` is `msm8974`, but every module include is gated by platform filters. Do not infer that simply setting `TARGET_BOARD_PLATFORM=taro` converts this to modern Codec2.
- PE historical patches update obsolete `display_headers` / `LOCAL_COPY_HEADERS` handling and old OMX. They are **not** an Android17 Codec2/VNDK contract.
- The real `LF52406/mondrian_android_device_xiaomi_sm8450-common` `lineage-24.0` `common.mk` selects `init.qti.media.rc` and `init.qti.media.sh`, as well as device media Codec2 policy/config sourced from `hardware/qcom-caf/sm8450/audio/primary-hal/configs`. Vendor codec compatibility and the exact owner of those media init modules must be verified separately.
- The separate PE `hardware/qcom-caf/sm8450/media` (matrix row 143, historical `fdd06e10393d5c831bcb69b1a59c4509d5923f62`) uses Qualcomm QSSI14 OMX and legacy `product.mk` including `libmm-omxcore`, `libcodec2_vndk.vendor`, old HIDL vendor codec targets, and Stagefright debug overrides. It is **closer to Waipio** but not yet established as modern Android17-compatible.
- The AOSP17 reference manifest does **not** contain a `hardware/qcom/media` repo. Android17 `frameworks/av` and media packages/Codec2 interfaces must retain their official versioned contracts, including existing reviewed PE Codec2 source tests. They cannot automatically provide proprietary Qualcomm video/codec services.

## Decision and acceptance gates

**Keep the historical `hardware/qcom/media` only as an archive. Do not activate on mondrian, add an old OMX service or force legacy VNDK/Stagefright properties.**

1. Resolve the unique Android17 vendor codec/service and `init.qti.media` module owners for sm8450, with exact Soong module names and linker dependencies.
2. Audit secure/nonsecure Codec2 components, vendor `libcodec2` interfaces, firmware, allocator/gralloc buffer ABI and vendor VINTF together with display and camera.
3. Inspect current Android17 C2/OMX compatibility without claiming all OMX is globally unsupported: use actual service and vendor interfaces as evidence. Check Codec2 APEX/service visibility, SELinux and seccomp.
4. Retain old PE fixes only if the relevant defect exists in the selected modern implementation. No copied kernel/VNDK shim or blanket `debug.stagefright.ccodec=4`.
5. Run full Soong/Make product build, required codec tests, `checkvintf`, CTS/VTS and physical camera/video encode/decode, DRM, playback and suspend/resume on mondrian.

**Status:** legacy scope and modern dependency candidates identified; active vendor codec owner, Android17 Soong and runtime unverified. Do not modify working MistOS/LineageOS device trees as part of this audit.

**Next row:** `hardware/qcom/wlan` — AOSP17 does contain a directly maintained official project, so compare and integrate actual Android17 source rather than simply isolating an archive.
