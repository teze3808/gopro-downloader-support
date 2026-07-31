# Privacy Policy for GoPro Downloader

Effective date: July 29, 2026

GoPro Downloader is an independent Chrome extension that helps users download media from their own GoPro Cloud Media Library. It supports direct Chrome downloads and an optional runner operated by the user on their own computer or network-attached storage.

This project is not affiliated with, authorized by, endorsed by, or produced by GoPro, Inc.

## Information the Extension Processes

The extension processes information needed to perform its user-requested download functions:

- GoPro Cloud media metadata, including media identifiers, filenames, capture dates, file sizes, media types, dimensions, and durations.
- Temporary signed download URLs returned by GoPro for the user's media.
- The URL of a runner configured by the user.
- Extension settings, download status, batch status, and error messages.

The extension uses the user's existing signed-in GoPro browser session to request this information from GoPro. It does not read, store, or transmit the user's GoPro password. It does not copy or store GoPro authentication cookies.

## How Information Is Used

The processed information is used only to:

- List media available in the user's GoPro Cloud Media Library.
- Create filenames and folders for downloaded media.
- Request original media download URLs.
- Download files through Chrome when the user selects Chrome download mode.
- Send inventory records and temporary signed URLs to a runner selected and operated by the user.
- Compare cloud inventory with files already present on the user's runner.
- Display progress, retries, and errors.

## Storage and Retention

Extension settings, recent status information, diagnostic logs, and Chrome download batch status are stored locally using Chrome extension storage. Up to 2,000 recent diagnostic entries are retained so download activity can be reviewed even when the extension popup is closed. Chrome's own download records are managed by Chrome.

The extension can create a local diagnostic JSON file only when the user clicks the export control. The file can contain redacted settings, environment information, recent errors, runner status, and batch summaries. Signed URL query parameters, passwords, cookies, tokens, authorization values, and other common secrets are removed automatically. The user decides whether to share the exported file.

When runner mode is used, inventory files, batch manifests, logs, temporary signed URLs, and downloaded media may be stored in folders controlled by the user on their own computer or NAS. Their retention is controlled by the user.

The project developer does not operate a server that receives or stores the user's GoPro media, inventory, signed URLs, runner address, settings, or download history.

## Sharing and Transfers

The extension does not sell user data and does not share it for advertising, profiling, credit assessment, or unrelated purposes.

Information is transferred only as needed for the extension's single purpose:

- To GoPro, when the signed-in browser requests the user's media inventory and download URLs.
- To Chrome's download system, when the user chooses direct Chrome downloads.
- To the runner address explicitly entered by the user, when the user chooses runner mode.

The optional Ko-fi link opens Ko-fi only after the user clicks it. No GoPro media information is sent to Ko-fi by the extension.

## Security

Remote runner connections must use HTTPS. Plain HTTP runner connections are allowed only to `localhost` or `127.0.0.1` on the same computer.

Temporary signed URLs can grant short-lived access to media. Users should protect exported manifests, runner logs, and runner storage, and should not expose the runner directly to the public internet.

## Analytics and Advertising

The extension contains no analytics, telemetry, advertising SDK, tracking pixel, or behavioral advertising.

## User Control

Users can:

- Stop an active download from the extension.
- Revoke runner-site access through Chrome's extension site-access settings.
- Clear diagnostic logs from the extension.
- Delete all locally saved extension logs, settings, and Chrome batch history from the extension.
- Clear all locally stored extension data by removing the extension.
- Delete downloaded media, inventory files, manifests, and logs from their own storage.

## Changes

Material changes to this policy will be published in this repository and reflected in the Chrome Web Store privacy disclosures before the changed data practice is released.

## Contact

Questions or privacy requests can be submitted through the project's GitHub issue tracker:

https://github.com/teze3808/gopro-downloader-support/issues
