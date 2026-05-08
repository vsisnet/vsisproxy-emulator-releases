# VsisProxy Emulator — Releases

Binary releases of [VsisProxy Emulator](https://github.com/REPLACE_OWNER/vsisproxy-emulator).

## Latest

See `version.json` for the latest version metadata (consumed by the in-app update checker).

## Layout

- `version.json` — manifest read by VsisProxy at startup (and via the "Kiểm tra cập nhật" button)
- `vX.Y.Z/` — full publish output for that version (run `VsisProxy.App.exe` as Administrator)

## Cài đặt

1. Tải zip phiên bản mới nhất từ Releases tab
2. Giải nén
3. Right-click `VsisProxy.App.exe` → Run as Administrator

App cần Administrator để load WinDivert64.sys driver (cho leak prevention features).

## License

Internal — Vsis.Net © 2026
