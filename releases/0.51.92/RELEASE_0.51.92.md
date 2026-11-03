# Arc 0.51.92 — Protected desktop release

The Windows installer now packages a transformed copy of Arc's application code, instead of readable development JavaScript. Production builds minify first-party modules, rename internal variables and obscure strings in selected reasoning and prompt-authoring modules. Development files remain unchanged. Third-party libraries retain their license notices and normal behavior.

Electron validates the application archive against a hash embedded in the executable and requires loading the application from that archive. Production builds also disable Node environment-option injection and command-line inspector activation. The Hey Arc widget now explicitly disables Developer Tools, matching the main production window.

Coding tools, SSH, local workflows, browser automation, custom tools and model execution remain available. Electron's Node helper mode stays enabled because those features depend on it. Signed update manifests remain in use. These measures make casual inspection and package modification harder; they do not make local code secret or impossible to reverse-engineer. This release does not add licensing, online activation or debugger traps.

The build runs checks against the transformed runtime and repeats packaged helper checks after the final executable fuses are set. Source maps, local test profiles, build manifests and original source copies are excluded from the first-party release payload. Windows Authenticode signing is separate from signed update manifests and requires a publisher signing certificate.
