---
layout: default
title: IDE Extensions
nav_order: 3
has_children: true
---

# IDE Extensions
{: .no_toc }

The set of extensions that integrates the API-design essential tools into their everyday work.
{: .fs-6 .fw-300 }

<div align="center">
    <img src="/ide-extensions/quick.svg" width="100px" style="margin-right: 30px">
    <img src="/ide-extensions/api-hub-logo.svg" width="100px" style="margin-right: 30px">
</div>

<br>

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

<br>

## Summary

These set of extensions were built with a cooperation mindset between them: they increase the capabilities of each other, including [markdownlint](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint) and [Spectral](https://marketplace.visualstudio.com/items?itemName=stoplight.spectral).

The [API hub](#api-hub) provides the rating of the modules evaluated on the [certification service](link), giving you real-time insights into what score to expect. For every rule broken, you can easily open the respective file/line on your IDE! And it works with Visual Studio Code and IntelliJ.

To work, the API hub uses an in-IDE-integrated SPA, that displays visual information about the broken rules and uses bidirectional communication with your API files. Check this gif to see a preview:

!image[preview-gif]

In the **VS Code**, you can take advantage of the [Spectral quick fix](#spectral-quick-fix) plugin to easily correct the linted errors. See in the previous gif how errors are corrected with a click!

<span class= "d-flex mt-5">
  [<img src="/certification-system/github-logo-gradient.png" width="30px" style="vertical-align: middle;"> Check the repository!](https://github.com/inditex/cac-opencertificationplugins){: .btn .mx-auto  .mt-2 .mb-4}
</span>

{: .note}
For these tools to work, **you must deploy the Certification microservice**: the main piece of the certification system. Learn how in the [documentation](link).

## SPA deployment

You need to have the SPA running so that the extensions are working seamesly. To do so, you need to deploy the SPA locally with the following commands:

1. Clone the repository:

{: .ml-4}
```bash
git clone git@github.com:inditex/cac-opencertificationplugins.git
```


2. Place yourself in the correct directory:

{: .ml-4}
```bash
cd plugins/spa-apihub/code
```


3. Install the dependencies:

{: .ml-4}
```bash
npm i
```


4. Once the process finishes, start the SPA:

{: .ml-4}
```bash
npm run start
```

## Installation

To install the extensions, you just need to search for all the following extensions in the IDE's marketplace and download them.

[VS Code Marketplace](https://marketplace.visualstudio.com/VSCode){: .btn .btn-blue .ml-auto .mr-2}
[IntelliJ Marketplace](https://plugins.jetbrains.com/){: .btn .btn-purple .mr-auto}

