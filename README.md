# VsisProxy Emulator

Tool Windows gắn proxy HTTP/SOCKS5 cho các giả lập Android (LDPlayer, NoxPlayer, MEmu, BlueStacks) **không cần app thứ ba bên trong giả lập**, không cần WinDivert TCP redirect — proxy được inject thẳng vào Android system property qua ADB.

---

## 📦 Bản mới nhất: **v1.1.5** _(2026-05-16)_

| Cài đặt | Portable |
|---|---|
| **[⬇ VsisProxy-Setup-1.1.5.exe](https://github.com/vsisnet/vsisproxy-emulator-releases/releases/latest/download/VsisProxy-Setup-1.1.5.exe)** _(17 MB)_ | **[⬇ VsisProxy-1.1.5.zip](https://github.com/vsisnet/vsisproxy-emulator-releases/releases/latest/download/VsisProxy-1.1.5.zip)** _(21 MB)_ |
| Inno Setup wizard + Add/Remove Programs | Giải nén ra ổ bất kỳ, không cài |

> 💡 **Đã cài v1.1.4 trở lên?** Mở app, đợi 2s — sidebar sẽ tự hiện nút **🎉 Phiên bản mới 1.1.5**. Click 1 phát → updater tự download + ghi đè + relaunch app, **không cần chạy installer nữa**.

### Có gì mới ở v1.1.5

- **Import Proxy dialog redesign**: header rõ ràng, button spacing đẹp, DataGrid kết quả Check proxy realtime với status pill 5 màu (Pending → Checking → Alive / Dead / Timeout) + latency mỗi proxy
- **Close warning broader**: đóng app khi đang có local proxy server chạy ngầm → popup cảnh báo (trước đó chỉ warn khi có emu pinned, bỏ sót case external tool đang dùng `127.0.0.1:port`)
- **Sidebar version label dynamic**: bind từ assembly metadata thay vì hardcode → mỗi release tự đúng

### Có gì mới gần đây

| Version | Ngày | Tóm tắt |
|---|---|---|
| 1.1.5 | 2026-05-16 | Import dialog UI + Check proxy realtime list + close warning broader + version label dynamic |
| 1.1.4 | 2026-05-16 | **In-place updater** — click 1 nút để cập nhật, không phải chạy lại installer |
| 1.1.3 | 2026-05-10 | Hỗ trợ tới 100 emu LDPlayer (port range 5555..5753) + fix Local Proxy không tự gắn lần đầu |
| 1.1.2 | 2026-05-10 | Fix TUN engine cho chrome/firefox (5 lỗi config sing-box 1.13) + Browser Profiles tab |
| 1.1.0 | 2026-05-10 | Tab Quản lý Game / App: pin từng process Windows qua proxy (sing-box + wintun TUN) |

📜 Changelog đầy đủ ở dưới — xem [📝 Lịch sử version](#-lịch-sử-version).

---

## 🚀 Điểm nổi bật

### 🔄 Chuyển proxy `ip:port:user:pass` → `ip:port` để dùng với tool khác

Đa số provider bán proxy dạng có authentication: `1.2.3.4:8080:myuser:mypass`. Nhưng nhiều tool (Selenium, ADBKit, browser extension cơ bản, automation script đơn giản) **không hỗ trợ proxy auth user/pass**, chỉ ăn được dạng `ip:port` không auth.

VsisProxy giải quyết bằng cách spin **local proxy server** cho từng proxy gốc:

```
Proxy gốc (auth):    1.2.3.4:8080:myuser:mypass     ← provider cấp
                              ↓
Local listener:      192.168.1.10:39999             ← VsisProxy tự tạo
                              ↓
        Tool khác paste 192.168.1.10:39999 vào field "Proxy"
        → Traffic tự được forward qua proxy gốc (kèm auth tự động)
        → Không lộ user/pass cho tool đó
```

**Click button "📋 Copy all local"** ở Proxy page → toàn bộ URL `ip:port` không auth được copy 1 phát vào clipboard, paste sang Excel / config tool khác / proxy list của bot dùng ngay.

### 🎯 Gắn proxy hàng loạt cho giả lập — Tự động hoặc thủ công

Có 2 chế độ:

**1. Tự động (Auto-assign)** — 1 tick chia đều proxy cho mọi giả lập online
- Tick checkbox `Tự động gán proxy` ở toolbar
- 50 emulator + 50 proxy → mỗi emu 1 proxy khác nhau (round-robin)
- 50 emulator + 10 proxy → mỗi proxy gán cho 5 emu, đều
- Tự gán cho emu mới khi Refresh, không cần thao tác lại

**2. Thủ công (Picker dialog)** — chọn chính xác proxy nào cho emu nào
- Click vào ô Proxy profile của 1 dòng → mở dialog có **search bar** + **danh sách sortable**
- Search theo nhãn / IP / port / type — tìm trong danh sách 1000+ proxy không khó
- Double-click row = chọn nhanh, có nút **Bỏ gán** để clear

Cả 2 chế độ đều **hot-apply** ngay — không cần Stop / Start engine, ADB push tự chạy nền, app trong emu thấy proxy mới sau ~1 giây.

---

## ✨ Tính năng

### Quản lý proxy
- **HttpProxyServer per-profile**: mỗi proxy profile có 1 listener cố định ở `host_lan_ip:port` — copy URL ra browser hoặc tool khác để dùng song song
- **Add / Import / Edit / Delete** proxy profiles, hỗ trợ HTTP CONNECT + SOCKS5 (RFC 1928 với pipelined greeting+auth+CONNECT, tiết kiệm RTT)
- **Bulk import** từ file text — định dạng phổ biến: `host:port`, `host:port:user:pass`, `user:pass@host:port`, `socks5://...`
- **Check Proxy** test alive/dead/timeout song song 8 connection cùng lúc
- **Search filter** realtime theo Name/Host/Port/Kind
- **Copy all local server**: copy 1 lúc nhiều URL `host:port` vào clipboard
- **Status pill** Alive xanh / Dead đỏ / Timeout / Not live

### Quản lý giả lập
- **Auto detect** LDPlayer 4/5/9, NoxPlayer, MEmu, BlueStacks 5 — đọc tên thật từ `leidian{N}.config` cho LDPlayer
- **Hỗ trợ tới 100 emu LDPlayer** mỗi lúc (port range 5555..5753) — discovery parallel TCP probe <1s
- **Auto-enable ADB Debug** cho mọi LDPlayer instance ngay khi app mở: watcher rescan 15s ghi `basicSettings.adbDebug=1` cho tất cả `leidian{N}.config` trên mọi install, kèm `FileSystemWatcher` cho config mới — emu mở sau không cần reboot
- **Stored bindings auto-apply lần đầu**: proxy đã pin từ session trước được ADB-push ngay khi mở app, không cần Stop/Start engine để nudge
- **Hot-apply binding**: chọn proxy từ picker dialog (search + sortable list) → tự áp dụng ADB `settings put global http_proxy host:port`, không cần Stop/Start engine
- **Auto-assign proxy** round-robin: 1 click chia đều proxies cho các emulator online
- **Show offline filter**: ẩn các emu stale trong ADB cache mặc định
- **Status pill** Online xanh / Offline đỏ / Port conflict vàng
- **Per-row Stop button** unbind proxy

### Quản lý Game / App PC (TUN engine)
- **Pin từng process Windows qua proxy**: chrome.exe, firefox.exe, game launcher, app bất kỳ → traffic của riêng process đó đi qua proxy chỉ định, các app khác giữ nguyên
- **Sing-box 1.13.11 + wintun 0.14.1** kernel-level TUN, gVisor TCP/IP stack userspace
- **Auto QUIC blocker**: tự thêm Windows Firewall rule chặn UDP/443 khi engine chạy → Chrome/Firefox fallback TCP HTTP/2 trong <1s, bỏ rule khi Stop
- **DNS hijack** qua sing-box internal resolver (1.1.1.1) — không leak DNS host khi TUN active
- **IPv6 reject** cho process pinned — ép fallback IPv4 qua proxy
- **Process picker dialog** từ `Win32_Process` snapshot

### Browser Profiles (Chrome multi-session)
- **Mỗi profile = 1 Chrome session độc lập**: cookie / login / extensions / lịch sử riêng (`--user-data-dir` per profile), gắn 1 proxy qua `--proxy-server` Chrome native flag
- **Multi-launch song song**: 5 profile = 5 cửa sổ Chrome, mỗi cái 1 IP khác nhau, không xung đột
- **Launch ↔ Stop toggle button**: 1 nút duy nhất trên mỗi row — click khi Stopped để mở Chrome, click khi Running (label đổi thành ■ Stop) để kill cả process tree (parent + 10+ renderer children atomic, không leave orphan)
- **Auto-detect status** mỗi 10s qua WMI command-line probe — đóng Chrome bằng X cũng tự sync UI

### Leak prevention (WinDivert)
- **Block DNS / QUIC / ICMP**: drop outbound UDP/53, TCP/53, TCP/853 (DoT), UDP/443 (HTTP/3) từ emu PIDs + ICMP unconditional
- **Block IPv6 leak**: drop IPv6 outbound từ emu PIDs (kể cả ICMPv6) — ép app fallback IPv4 qua proxy
- Đảm bảo IP của host không lộ qua các đường vòng

### Cập nhật + tài liệu
- **In-place updater (v1.1.4+)**: nút `🎉 Phiên bản mới X.Y.Z` chỉ xuất hiện trên sidebar khi có version mới. Click 1 phát → main app đóng, updater nhỏ tự download zip + giải nén + ghi đè + relaunch app — không phải chạy lại installer wizard
- **Auto check** sau 2s khi mở app, im lặng nếu đã ở bản mới nhất
- **Close warning**: hiển thị cảnh báo khi đóng app trong lúc có local proxy server / emu binding / TUN engine / Browser session đang chạy
- **Hard exit watchdog**: app không bị zombie sau khi đóng

---

## 💻 Yêu cầu cấu hình

| Hạng mục | Yêu cầu |
|---|---|
| OS | Windows 10 (1809+) hoặc Windows 11, **64-bit** |
| Quyền | **Administrator** (cần để load WinDivert64.sys driver cho leak prevention) |
| .NET | **.NET 8.0 Desktop Runtime (x64)** — [Tải tại đây](https://dotnet.microsoft.com/download/dotnet/8.0/runtime) |
| RAM | 2 GB free (tool nhẹ, RAM chính dành cho emulator) |
| Disk | ~30 MB cho app + log |
| Mạng | Internet để check update + sử dụng proxy upstream |

> ℹ️ Windows thường cài sẵn .NET Desktop Runtime. Nếu app báo thiếu khi mở, tải link trên và cài "**.NET Desktop Runtime 8.0.x** for Windows x64".

### Hỗ trợ giả lập

| Giả lập | Phiên bản | ADB tự động |
|---|---|---|
| LDPlayer 9 | mọi build | ✅ Auto-enable + reboot |
| LDPlayer 5 | mọi build | ✅ Auto-enable + reboot |
| LDPlayer 4 | mọi build | ✅ Auto-enable + reboot |
| NoxPlayer | 6.x, 7.x | ⚠️ Cần bật ADB thủ công lần đầu |
| MEmu | 7.x, 8.x | ⚠️ Cần bật ADB thủ công lần đầu |
| BlueStacks 5 | 5.x | ⚠️ Cần bật ADB thủ công trong Settings → Advanced |

---

## 📥 Cài đặt

### Cách 1: File cài đặt (khuyến nghị)

Cài đặt 1 click với wizard tiếng Việt — tự tạo shortcut + đăng ký Add/Remove Programs.

**[⬇ Tải VsisProxy-Setup-1.1.5.exe](https://github.com/vsisnet/vsisproxy-emulator-releases/releases/latest/download/VsisProxy-Setup-1.1.5.exe)**

1. Tải file `VsisProxy-Setup-1.1.5.exe` từ link trên
2. Right-click → **Run as administrator**
3. Theo wizard cài đặt (chọn ngôn ngữ Tiếng Việt nếu thích)
4. Chọn folder cài (mặc định `C:\Program Files\VsisProxy\`)
5. Tick "Tạo shortcut Desktop" nếu muốn
6. Bấm Cài đặt → khi xong tick "Khởi chạy VsisProxy Emulator" → Finish
7. **Cập nhật phiên bản mới**: từ v1.1.4 trở đi không cần chạy installer nữa — mở app, đợi 2s, sidebar tự hiện nút `🎉 Phiên bản mới X.Y.Z` → click → đợi ~30s là xong (bundled `VsisProxy.Updater.exe` lo phần còn lại)

**Gỡ cài**: Settings → Apps → "VsisProxy Emulator" → Uninstall (sẽ xoá luôn `%APPDATA%\VsisProxy\` chứa config + log).

### Cách 2: Bản Portable

Không cần quyền cài đặt vào Program Files — giải nén ra ổ bất kỳ, chạy luôn. Phù hợp khi:
- Bạn muốn chạy nhiều bản trên cùng máy
- Cần copy app sang máy khác qua USB
- Không có quyền Administrator để cài (nhưng vẫn cần admin để chạy app vì WinDivert)

**[⬇ Tải VsisProxy-1.1.5.zip](https://github.com/vsisnet/vsisproxy-emulator-releases/releases/latest/download/VsisProxy-1.1.5.zip)**

1. Tải file `VsisProxy-1.1.5.zip`
2. Giải nén ra folder bất kỳ (ví dụ `D:\Tools\VsisProxy\`)
3. Right-click `VsisProxy.App.exe` → **Run as administrator**
4. **Update sau này** (v1.1.4+): bundled `VsisProxy.Updater.exe` đã nằm trong zip → mở app, click nút `🎉 Phiên bản mới X.Y.Z` trên sidebar khi xuất hiện → updater tự download + ghi đè + relaunch. Không cần tải zip thủ công nữa.

> ⚠️ Cả 2 cách đều **bắt buộc Run as administrator** — WinDivert64.sys driver cần admin để load. Nếu chạy không-admin, các tính năng leak prevention sẽ tắt nhưng ADB inject vẫn work.

---

## ❤️ Ủng hộ team dev

VsisProxy Emulator là **miễn phí cho cộng đồng**. Nếu tool giúp được công việc của bạn, cách ủng hộ tốt nhất là **mua proxy chất lượng** từ chúng tôi:

### 🌐 [proxyvietnamgiare.vsis.net](https://proxyvietnamgiare.vsis.net/)

- **Proxy Việt Nam**: HTTP / SOCKS5, IP từ ISP lớn (Viettel, VNPT, FPT, MobiFone)
- **Proxy quốc tế**: residential + datacenter, 50+ quốc gia
- **Auth user/pass**: an toàn, không lộ IP
- **Uptime 99.9%**, hỗ trợ 24/7
- **Giá rẻ nhất thị trường**, gói lẻ + bulk theo nhu cầu

### Liên hệ

- 🌐 Website: [vsis.net](https://vsis.net)
- 📚 Tài liệu / kiến thức proxy: [doc.vsis.net/kien-thuc-ve-proxy](https://doc.vsis.net/kien-thuc-ve-proxy/)
- 🔌 Mua proxy: [proxyvietnamgiare.vsis.net](https://proxyvietnamgiare.vsis.net/)
- 🐛 Báo lỗi: [Issues tab](https://github.com/vsisnet/vsisproxy-emulator-releases/issues)

---

## 📋 Layout repo này

- `version.json` — manifest đọc bởi in-app update checker (auto cập nhật khi push commit)
- `vX.Y.Z/` — full publish output cho từng version (browseable trên GitHub)
- `Releases tab` — chứa file installer `.exe` + portable `.zip` cho mỗi version

## 📝 Lịch sử version

| Version | Highlight |
|---|---|
| **1.1.5** | Import Proxy dialog redesign: header tiêu đề + DataGrid kết quả Check proxy realtime (Status pill 5 màu + Latency) + button spacing đẹp • Close warning trigger broader: bất kỳ khi có proxy trong list → popup cảnh báo proxy sẽ ngừng (không cần emu pinned) • Sidebar `Phiên bản: X.Y.Z` bind dynamic từ assembly version, không còn hardcode |
| 1.1.4 | In-place updater: cập nhật 1 click không cần chạy lại installer • Sidebar `Kiểm tra cập nhật` ẩn mặc định, chỉ hiện `🎉 Phiên bản mới X.Y.Z` khi có update • Bundled `VsisProxy.Updater.exe` tự download zip + giải nén + ghi đè + relaunch main app |
| 1.1.3 | Hỗ trợ tới 100 emu LDPlayer (port range 5555..5753) • Fix Local Proxy không tự gắn lần đầu (`StartupBootAsync` push ADB ngay sau discover) • ADB watcher rescan 60s → 15s • UI Game/App rename `Start TUN Engine` → `Start Proxy` • Browser tab Launch ↔ Stop toggle với atomic process-tree kill |
| 1.1.2 | Fix TUN engine cho chrome/firefox: 5 lỗi config sing-box 1.13 (legacy `sniff` field, UDP-not-supported reject silent, DNS hijack rỗng, `detour:direct` deprecated, IPv6 svchost loop) • Browser Profiles tab mới (sidebar 🧭) • QuicBlocker firewall rule UDP/443 |
| 1.1.1 | Hotfix `ProcessPickerDialog` NRE crash khi mở dialog "Thêm process" |
| 1.1.0 | Tab Quản lý Game / App: pin từng process Windows qua proxy via sing-box + wintun TUN engine |
| 1.0.2 | Multi-engine LDPlayer support (VBox UUID disambiguation), port-collision detection, auto-check proxy |
| 1.0.1 | First public release

## 📜 License

Internal — Vsis.Net © 2026. Source code private.
