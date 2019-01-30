# Version release

## Version number

The system adopts a three-digit versioning scheme. The three digits respectively are major version number, minor version number, and revision number, for example: 5.1.2. 

For more information, see the [http://semver.org/lang/zh-CN/](http://semver.org/lang/zh-CN/).

- Major version number: All versions in the major version number must be compatible with each other. It is not necessary to be fully compatible with other major version numbers, but it is best to have backward compatibility.
- Minor version number: Represents new feature enhancements. The larger the version number, the richer the feature.
- Revision number: Represents the BugFix version. The revision number is only for bug fixes. The larger the version number, the more stable it is.

## Version maintenance

You can maintain up to two versions at the same time.

For example, the current trunk is `5.3.0`, then the bugfix branch of `5.2.x` will be maintained. When any bugs arise in `5.1.x`, users are prompted to upgrade the system.

## Release process

- The daily development branch uses the SNAPSHOT version, for example: `5.3.0-SNAPSHOT`.
- When it comes to official release, you can modify the version to official version, for example: `5.3.0`.
- Pull up the next version after release, for example: `5.3.1-SNAPSHOT`.
