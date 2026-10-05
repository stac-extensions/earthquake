# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- Updated Magnitude Type definition with 41 USGS types as described in the official [API metadata](https://earthquake.usgs.gov/fdsnws/event/1/application.json).

### Changed

- Magnitude now allows [negative values](https://www.usgs.gov/faqs/how-can-earthquake-have-a-negative-magnitude).

### Deprecated

### Removed

### Fixed

- Allow generic moment magnitude (`mw`) in `eq:magnitude_type`, document its meaning, and add a USGS validation example.

## [1.0.0] - 2025-01-20

Initial release of the Earthquake extension.

[Unreleased]: <https://github.com/stac-extensions/earthquake/compare/v1.0.0...HEAD>
[1.0.0]: <https://github.com/stac-extensions/earthquake/tree/v1.0.0>
