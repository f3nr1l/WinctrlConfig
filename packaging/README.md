# Packaging

App-id: `io.github.f3nr1l.WinctrlConfig`. Shared assets in this directory:

- `io.github.f3nr1l.WinctrlConfig.desktop` — desktop entry (`@BINARY@` is
  replaced with the binary path/name at install time).
- `io.github.f3nr1l.WinctrlConfig.metainfo.xml` — AppStream metadata (fill in the
  real URLs and screenshots before submitting to a store).
- `icons/hicolor/**` — application icons.

## Local install (no packaging)

`./install.sh` installs the release binary, icons, desktop entry and translation
catalogs into `~/.local` for the current user (no `sudo`).

## Flatpak

See `flatpak/`. Build and install locally:

```sh
cd flatpak
flatpak-builder --user --install --force-clean build io.github.f3nr1l.WinctrlConfig.yml
flatpak run io.github.f3nr1l.WinctrlConfig
```

Notes:
- Requires the GNOME 51 SDK and the Rust extension of the same freedesktop base
  (`flatpak install flathub org.gnome.Sdk//51
  org.freedesktop.Sdk.Extension.rust-stable//26.08`). When bumping the runtime,
  bump the extension branch with it: an older branch ships an older `rustc`.
- The manifest builds with network access (cargo fetches crates). For a fully
  offline / Flathub-style build, drop `--share=network` from `build-args` and add a
  generated cargo sources file:
  ```sh
  python3 flatpak-cargo-generator.py ../../rust/Cargo.lock -o cargo-sources.json
  ```
  then reference `cargo-sources.json` under the module `sources:` and add
  `--offline` to the cargo command. (`flatpak-cargo-generator.py` comes from the
  flatpak-builder-tools repository.)

## AppImage

See `appimage/build-appimage.sh`. It downloads `linuxdeploy` and
`linuxdeploy-plugin-gtk`, builds the release binaries, assembles an AppDir
(binaries, desktop entry, icons, metadata, translations) and bundles the GTK 4 /
libadwaita stack into a single `.AppImage`. Requires a Rust toolchain, the
gtk4/libadwaita development files, `msgfmt`, `curl` and FUSE.

```sh
./appimage/build-appimage.sh
```

## Build notes (0.9.0)

Both packages build and install successfully:

- **AppImage** — `packaging/appimage/build-appimage.sh` (set `APPIMAGE_EXTRACT_AND_RUN=1`
  on hosts without FUSE). Produces `WinCtrl_Config-x86_64.AppImage`.
- **Flatpak** — `flatpak-builder` against the manifest.

The manifest targets `org.gnome.Platform//51` and builds with `rust-stable`; no
nightly toolchain is needed (gtk4-rs 0.11 requires `rustc` 1.92).
