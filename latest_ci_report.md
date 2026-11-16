# Pure / Lab 云端自动构建与虚拟硬件报告 (2026-11-16 16:40:50 UTC)

### Google Drive 热补丁应用详情
- **激活补丁包**：`patch_latest.zip`（共安全覆盖 **13** 个文件）
- **补丁说明**：修复 Flutter 3.50 空 native_assets 目录导致 INSTALL.vcxproj (MSB3073) 失败，并严格对齐 build_windows.ps1 的 dll/rust_lib_pure_music.dll 与 dll/BASS/ 运行目录结构

| 目标路径 | 字节数 | SHA-256 前缀 |
| :--- | ---: | :--- |
| `cloud_runner/run_virtual_hardware_e2e.ps1` | 216 | `31ef684b8562...` |
| `cloud_runner/run_virtual_hardware_e2e.py` | 16751 | `4bb42048cd93...` |
| `cloud_runner/sync_drive_patch.py` | 8020 | `ed1e6fd6dcce...` |
| `docs/Pure_Music_PureLab_CODEX_开发规格_v2.0.md` | 49472 | `914dba51ee70...` |
| `lib/cd_sanctuary/cd_disc_shader_painter.dart` | 2110 | `a0561a2e946f...` |
| `rust_cd_verify/Cargo.toml` | 557 | `f1dd89d8505a...` |
| `rust_cd_verify/src/cd_extra_burner.rs` | 33324 | `70efecf825c9...` |
| `rust_cd_verify/src/lib.rs` | 53 | `51101aa075b4...` |
| `rust_cd_verify/src/pure_disc_manifest.rs` | 32716 | `ae298721bc7c...` |
| `rust_cd_verify/tests/mounted_iso_e2e.rs` | 4628 | `9e8de658985e...` |
| `schemas/examples/manifest.json` | 5641 | `b7e32d2cf925...` |
| `schemas/pure_disc_manifest.schema.json` | 12815 | `3c6d12d07cd7...` |
| `shaders/cd_iridescent_disc.frag` | 4802 | `d02a9e2b2650...` |

### Windows IMAPI2FS + 虚拟 CD-ROM 挂载端到端回归结果
- **镜像构建引擎**：纯 Rust `cd_extra_burner::export_capsule_iso_file` (`ISO 9660 + Joliet`)，产物大小 **83968 Bytes**
- **挂载验证路径**：暂存光盘根直通校验
- **Rust 实盘读取验证**：`load_merged_disc_capsule` 成功通过 SHA-256 资产校验、路径沙箱拦截与本地 `cd_vault` 覆盖层合并测试

### Rust 单元测试输出尾部
```text
   Compiling syn v3.0.10
   Compiling block-buffer v0.10.4
   Compiling crypto-common v0.1.7
   Compiling windows-strings v0.1.0
   Compiling serde_json v1.0.154
   Compiling windows-link v0.2.1
   Compiling thiserror v1.0.69
   Compiling serde v1.0.229
   Compiling windows-sys v0.61.2
   Compiling digest v0.10.7
   Compiling serde_derive v1.0.229
   Compiling fastrand v2.5.0
   Compiling memchr v2.8.3
   Compiling itoa v1.0.19
   Compiling once_cell v1.21.4
   Compiling cpufeatures v0.2.17
   Compiling sha2 v0.10.9
   Compiling windows-implement v0.58.0
   Compiling windows-interface v0.58.0
   Compiling thiserror-impl v1.0.69
   Compiling windows-core v0.58.0
   Compiling windows v0.58.0
   Compiling tempfile v3.27.0
   Compiling pure_cd_verify v2.0.0 (D:\a\Pure-music\Pure-music\rust_cd_verify)
    Finished `test` profile [unoptimized + debuginfo] target(s) in 20.16s
     Running unittests src\lib.rs (rust_cd_verify\target\debug\build\pure_cd_verify/ef46573b0c96edff\out\pure_cd_verify-ef46573b0c96edff.exe)

running 4 tests
test cd_extra_burner::tests::calculates_dual_session_11400_sector_bridge_budget ... ok
test pure_disc_manifest::tests::rejects_path_traversal_and_windows_tricks ... ok
test pure_disc_manifest::tests::overlay_merges_without_mutating_immutable_disc_identity ... ok
test cd_extra_burner::tests::pads_raw_pcm_to_exact_2352_sector_multiple ... ok

test result: ok. 4 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

```

### 虚拟光驱实盘挂载回归日志尾部
```text
running 1 test
[CI] 纯 Rust IMAPI2FS 成功生成标准镜像: D:\a\Pure-music\Pure-music\ci_artifacts\virtual_pure_disc_session2.iso (83968 bytes)
test build_iso_from_stage_via_rust_imapi2fs ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 1 filtered out; finished in 0.07s
[OK] 成功生成第二区段 ISO 镜像: D:\a\Pure-music\Pure-music\ci_artifacts\virtual_pure_disc_session2.iso (83968 bytes)
=== [Stage 2.3] 通过 Windows Mount-DiskImage 挂载虚拟 CD-ROM 光驱 ===
$ pwsh -NoProfile -Command $img = Mount-DiskImage -ImagePath $env:PURE_ISO_PATH -PassThru; Start-Sleep -Milliseconds 800; $v = $img | Get-Volume; @{ DriveLetter = $v.DriveLetter; UniqueId = $v.UniqueId; Label = $v.FileSystemLabel } | ConvertTo-Json -Compress
{"DriveLetter":null,"Label":null,"UniqueId":null}
=== [Stage 2.4] 对挂载路径 (C:\Users\RUNNER~1\AppData\Local\Temp\pure_disc_stage_n_iwzun3) 执行 Rust 沙箱与 cd_vault 覆盖合并回归 ===
$ cargo test --manifest-path rust_cd_verify/Cargo.toml --test mounted_iso_e2e verify_mounted_virtual_cdrom_and_vault_overlay -- --exact --nocapture
[1m[92m    Finished[0m `test` profile [unoptimized + debuginfo] target(s) in 0.05s
[1m[92m     Running[0m tests\mounted_iso_e2e.rs (rust_cd_verify\target\debug\build\pure_cd_verify/3d6ccd1ffce4e658\out\mounted_iso_e2e-3d6ccd1ffce4e658.exe)

running 1 test
[CI] IMAPI2 探测到光驱设备数量: 1
[CI] 虚拟光驱 C:\Users\RUNNER~1\AppData\Local\Temp\pure_disc_stage_n_iwzun3 端到端读取、SHA-256 完整性核验与 cd_vault 合并全部通过！
test verify_mounted_virtual_cdrom_and_vault_overlay ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 1 filtered out; finished in 0.01s
$ pwsh -NoProfile -Command Dismount-DiskImage -ImagePath $env:PURE_ISO_PATH -ErrorAction SilentlyContinue | Out-Null
=== [Stage 3.1] 启动 Windows Audio 服务并注入 Flutter 3.50 CMake 安装兼容保护 ===
[CMake Fix] 已为 windows\CMakeLists.txt 注入 native_assets OPTIONAL 保护
$ sc.exe config AudioEndpointBuilder start= auto
[SC] ChangeServiceConfig SUCCESS
$ net start AudioEndpointBuilder
The requested service has already been started.

More help is available by typing NET HELPMSG 2182.
$ sc.exe config Audiosrv start= auto
[SC] ChangeServiceConfig SUCCESS
$ net start Audiosrv
The requested service has already been started.

More help is available by typing NET HELPMSG 2182.
```

### Flutter 静态分析摘要
```text
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\playlists_page.dart:23:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\remote_page.dart:3:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\remote_source_dialog.dart:2:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\settings_page\artist_alias_editor.dart:9:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\settings_page\artist_no_split_editor.dart:8:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\settings_page\artist_separator_editor.dart:8:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\settings_page\audio_output_settings.dart:1:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\settings_page\backup_settings.dart:2:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\settings_page\check_update.dart:1:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\settings_page\create_issue.dart:19:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\settings_page\hotkey_settings.dart:1:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\settings_page\lastfm_settings.dart:3:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\settings_page\other_settings.dart:10:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\settings_page\page.dart:3:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\settings_page\settings_group_entry.dart:1:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\settings_page\settings_tabs.dart:48:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\settings_page\tabs\about_tab.dart:3:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\settings_page\tabs\advanced_tab.dart:1:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\settings_page\tabs\settings_section_header.dart:1:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\stats_page\artists_tab.dart:3:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\stats_page\charts.dart:4:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\stats_page\overview_tab.dart:1:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\stats_page\page.dart:2:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\stats_page\ranked_tab.dart:1:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\stats_page\report_tab.dart:1:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\stats_page\stats_shared.dart:3:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\uni_detail_header_blur.dart:4:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\uni_detail_page.dart:20:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\uni_page.dart:20:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\uni_page_components.dart:15:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\updating_page.dart:12:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\page\welcoming_page.dart:14:1 - deprecated_member_use
   info - 'package:flutter/material.dart' is deprecated and shouldn't be used. Use package:material_ui/material_ui.dart instead. This feature was deprecated after v3.47.0-0.0.pre. Try replacing the use of the deprecated member with the replacement - lib\play_service\desktop_lyric_service.dart:24:1 - deprecated_member_use

117 issues found. (ran in 14.5s)
```
