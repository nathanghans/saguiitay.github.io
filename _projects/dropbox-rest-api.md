---
title: "Dropbox REST API"
excerpt: "DropboxRestAPI is a C# client for Dropbox"
author_profile: false
header:
  teaser: github-logo.png
sidebar:
  - title: 
    text: "[GitHub](https://github.com/saguiitay/DropboxRestAPI){: .btn .btn--large .btn--success}"
  - title: 
    text: "[NuGet](https://www.nuget.org/packages/DropboxRestAPI/){: .btn .btn--large .btn--success}"
---

**Watch out!** This project and nuget are obsolete with Dropbox's release of an official SDK for C#: [https://github.com/dropbox/dropbox-sdk-dotnet](https://github.com/dropbox/dropbox-sdk-dotnet).
{: .notice}

DropboxRestAPI is a C# client for Dropbox service through its RESTful API, with support for both the Core and the Business APIs.

Dropbox haven't released an official .Net/C# library, but there are several alternatives, including mine, which can be found in their documentation page [https://www.dropbox.com/developers/core/sdks/other](https://www.dropbox.com/developers/core/sdks/other)

[https://github.com/saguiitay/DropboxRestAPI](https://github.com/saguiitay/DropboxRestAPI)


## Nuget Package

To install DropboxRestAPI , run the following command in the Package Manager Console

```
Install-Package DropboxRestAPI  
```

## Features

  - Full Async support
  - Full implementation of the Core API: 
    - Support for OAuth 2
    - Support for Metadata operations, including PUT upload, chunked uploads and chunked download
  - Full implementation of the new Business API
  
## Release Notes

  - 1.2.1 Added support for the Business Group Info endpoint.
  - 1.2.0 Fixed SharedFolder methods. Breaking Changes: 
    - Authorize is no longer an async method
    - Split SharedFolders method into two methods SharedFolders and SharedFolder
  - 1.1.8 Added some fields to account information. Added support for groups in SharedFolder.
  - 1.1.7 Improved handling of cancellation tokens.
  - 1.1.6 Fixed usage of SaveUrlJobAsync. Memory/Performance improvements.
  - 1.0.5 Fixed usage of 'cursor' parameter. Added support for SharedFolder information.
  - 1.0.5 Fixed usage of 'cursor' parameter. Added support for SharedFolder information.
  - 1.0.4 Fixed issues with downloading of large files.
  - 1.0.3 Fixed issues with downloading of zero-bytes files.
  - 1.0.0 Initial release.
  

