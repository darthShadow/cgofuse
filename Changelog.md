# Changelog


**v1.6.0**

- Rename import path to `github.com/winfsp/cgofuse`.

- Convert package to module.

- Support FUSE3 on Linux and FreeBSD. By default, cgofuse will link with FUSE2. To link with FUSE3 add `-tags=fuse3` to your `go build` flags.

- Support FUSE-T on macOS.

- Preliminary support for Windows on ARM64.

- Support Linux distributions that place `fusermount` in `/usr/bin`.

- Add `FileSystemChmod3` interface. `Chmod3` is similar to `Chmod` except that it includes a file handle that is available only under FUSE3.

- Add `FileSystemChown3` interface. `Chown3` is similar to `Chown` except that it includes a file handle that is available only under FUSE3.

- Add `FileSystemUtimens3` interface. `Utimens3` is similar to `Utimens` except that it includes a file handle that is available only under FUSE3.

- Add `FileSystemRename3` interface. `Rename3` is similar to `Rename` except that it includes flags that are available only under FUSE3. These flags include `RENAME_NOREPLACE` and `RENAME_EXCHANGE`.

- Add `FileSystemGetpath` interface. A case-insensitive file system can use `Getpath` to report the correct case of a file path on Windows.

- Add `FileSystemHost.SetCapDeleteAccess`. A file system can use this capability to deny delete access on Windows. Such a file system must:
    - Implement the `Access` file system operation and handle the new `fuse.DELETE_OK` mask to return `-fuse.EPERM` for files that should not be deleted. An example implementation might look like:
        ```Go
        func (fs *filesystem) Access(path string, mask uint32) int {
            if "windows" == runtime.GOOS {
                if 0 != mask&fuse.DELETE_OK {
                    if "/nounlink" == path {
                        return -fuse.EPERM
                    }
                }
                return 0
            } else {
                return -fuse.ENOSYS
            }
        }
        ```
    - Return `-fuse.EPERM` from `Unlink` / `Rmdir` for files that should not be deleted.

- Add `FileSystemHost.SetCapOpenTrunc`. A file system can use this capability to inform the host that it can handle the `O_TRUNC` flag.

- Add `FileSystemHost.SetDirectIO`. A file system can use this capability to disable page caching on FUSE3.

- Add `FileSystemHost.SetUseIno`. A file system can use this capability when it wants the host to use the `ino` values reported by `Getattr` and other operations on FUSE3.


**v1.5.0**

- Add `FileSystemHost.Notify` API which allows file change notification to be issued from the user mode file system [Windows only].
- Add `notifyfs` file system to showcase the new API functionality [Windows only].


**v1.4.0**

- The FUSE library is demand-loaded on all platforms.
    - Prior to this version only the Windows FUSE library (WinFsp) was demand loaded.
    - This introduces a behavior change for programs that use FUSE when FUSE is not present. Prior to this version such programs would not start at all. In this new version such programs will start, but will fail with a `panic` in `FileSystemHost.Mount` if FUSE is not present.


**v1.3.0**

- Add FileSystemOpenEx interface.
- Miscellaneous other fixes and improvements.


**v1.2.0**

- Cgo and !cgo variants for Windows port.
- FreeBSD, NetBSD and OpenBSD ports.


**v1.1.0**

- `OptParse` function parses FUSE options.
- `fmt.Stringer` and `fmt.GoStringer` implementation for `fuse.Error`.


**v1.0.4**

- Implement BSD `flags`, `chflags`, `setcrtime`, `setchgtime`.
- Improve documentation.


**v1.0.3**

- Windows XP compatibility (eliminate `RegGetValueW`).


**v1.0.2**

- Windows XP compatibility (eliminate slim R/W lock).


**v1.0.1**

- Cross-compilation `Dockerfile`.
- CircleCI integration.
- Do not catch `SIGHUP`.
- Improve documentation.


**v1.0**

- Initial cgofuse release.
- The API is now **FROZEN**. Breaking API changes will receive a major version update (`2.0`). Incremental API changes will receive a minor version update (`1.x`).
