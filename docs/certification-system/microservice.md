---
layout: default
title: Microservice
parent: Certification System
nav_order: 1
---

# Certification service
{: .no_toc }

The microservice responsible for getting a grade for each API.
{: .fs-6 .fw-300 }

<br>

<div >
    <img src="/certification-system/cert-image.png" width="20%" style="float: right;"> 
</div>

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

<br>

## Summary

API Certification is the microservice responsible for getting a grade for each API. It has several modules to certify different aspects of each API and calculate a final score for them using a weighted average.

This open-source API-First-based certification service evaluates your APIs according to a set of rules that the user can customize.

{: .note }
The microservice has a set of rules that are in the system **by default**. In addition, you can sync it with the [rulesets repository](link) to override the service default rules with a more-complete set.

We want to make it simple. In the end, each certified API will be broken down into a single grade, which will mean how well-design your API is. 

If you want to know if your API complies with your design rules, if it addresses some of the OWASP vulnerabilities, and if it complies with documentation guidelines… this is your service.

{: .note }
Due to an [issue in protolint](https://github.com/yoheimuta/protolint/issues/144), the certification of gRPC APIs in Windows it is not possible_.

## Installation

Know that we recommend the use of [Node v18.13.0](https://nodejs.org/es/blog/release/v18.13.0/) to work on this project. 

Deploy the service following these steps: 

1. Clone the repository:

    ```zsh
    git clone git@github.com:inditex/mic-openapicertification.git
    ```

2. Place yourself in it: 

    ```zsh
    cd mic-openapicertification/packages
    ```

3. Install the dependencies: 

    ```zsh
    npm install
    ```

4. Optionally, add your GitHub credentials one of the following ways to be able to validate private repositories:

   - as environment variables:

      ```zsh
      CERWS_GH_USERNAME # GitHub username.
      CERWS_GH_PASSWORD # GitHub personal access token.
      ```
   - or in the [`configmap.yml` file](code/config/configmap.yml):
      ```yml
      cerws:
        common:
          rest:
            client:        
              github-rest-client:
                username: <GITHUB_USERNAME>
                password: <GITHUB_PERSONAL_ACCESS_TOKEN>
      ```

5. Run the service: 

    ```bash
    npm start
    ``` 

<br>

You can make use of this service by just making a request to its [API](Link-a-la-API-en-el-repo) or, even better, using the [apicli](Link-al-repo) CLI tool that we have developed for this matter.

[Check the API](Link-a-la-API-en-el-repo){: .btn .btn-green .ml-auto .mr-2 .mt-2 .mb-2}
[Check the CLI](Link-al-repo){: .btn .btn-purple .mx-auto .mt-2 .mb-2}


You can also use the [API hub](link) set of tools from your IDE to help you design your API at the same time you validate it with the service. The API hub provides the rating of the modules evaluated on the [certification service](link), giving you real-time insights into what score to expect.
{: .mb-4}

{: .highlight}
You can check the **available enpoinds** in the [API](/certification-system/api/) section.

<br>

## Performance and configuration

As you have previously read, the microservice works with a compound of rules. It can be the default one in this current repository, or the ones in the [Rulesets repository](link).


<span class= "d-flex">
  [Default rulesets](/certification-system/rulesets/){: .btn .mx-auto  .mt-2 .mb-2}
</span>

If an API doesn't break any rule, it gets an A+. If there are rules broken, this grade goes down accordingly. To calculate so, we have divided the scoring system into three modules (Design, Security, and Documentation) that assign grades on a letter scale from D to A<sup>+</sup>.


| Score | Letter |
|:---:|:---:|
|100|A<sup>+</sup>|
|90 - 99|A|
|75 - 89|B|
|50 - 74|C|
|0 - 49|D|


Each module is evaluated separately, and the global grade is calculated by taking a weighted average of the grades from the different modules.

<table>
<tr><th>Weights for REST APIs </th><th>Weights for AsyncAPI and gRPC APIs</th></tr>
<tr><td>
<table>
  <thead>
    <tr> 
      <th colspan="2">Module</th>
      <th colspan="2">Weight</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td colspan="2">Desing</td>
      <td colspan="2">0.4</td>
    </tr>
    <tr>
      <td colspan="2">Security</td>
      <td colspan="2">0.45</td>
    </tr>
    <tr>
      <th rowspan="4">Documentation</th>
      <td rowspan="4">0.15</td>
      <th>Convention rules</th>
      <td>0.30 </th>
    </tr>
    <tr>
      <th>Custom rules</th>
      <td>0.70</td>
    </tr>
  </tbody>
</table>
</td><td>

<table>
  <thead>
    <tr>
      <th colspan="2">Module</th>
      <th colspan="2">Weight</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td colspan="2">Design</td>
      <td colspan="2">0.85</td>
    </tr>
    <tr>
      <td colspan="2">Security</td>
      <td colspan="2">N/A</td>
    </tr>
    <tr>
      <th rowspan="4">Documentation</th>
      <td rowspan="4">0.15</td>
      <th>Convention rules</th>
      <td>0.30 </th>
    </tr>
    <tr>
      <th>Custom rules</th>
      <td>0.70</td>
    </tr>
  </tbody>
</table>
</td></tr> </table>

>*NOTE: For the Documentation module, different weights are assigned base and custom rules. To calculate the Documentation module score, the service operates with a weighted average of the two kinds of rules.*

Each module's rules can be whether warnings, errors, or information. The breach of each of them means:

- **Warning**: This is not compliant with the guidelines, or the contract is not fully documented, but it's still valid — they have a lower impact on grade.
- **Error**: Failures of this severity might severely impact the usage of this contract as the source of truth, or it's not valid at all — they have a higher impact on the grade.
- **Information**: Suggestions on how to improve your API documentation that doesn't mean it's poorly documented — They have no impact on the grade.

A correction factor is added for error-type rules breach, assigning them a higher weight.

> *NOTE: For more information regarding the scoring, check the [Scoring.md](/Scoring.md) file!*


You can **modify the score calculation** by adjusting some parameters in the [`configmap.yml` file](code/config/configmap.yml) :

- If you want to increase the weight of errors, you can increase the value of the `error-coefficient-weight` property, which by default is `5`.

- If you want to modify the weight of the modules, you can change them in the `modules-weights` and `modules-weights-without-security` properties to adjust them to your needs.

- You can also modify in the Documentation module the weights of the base and custom rules in `rules-weights`.

You can **modify the whole set of rules** in the `lint-repository-folder` property. The default route is the set of rules in this repository, but you can change it to the Rulesets repository. 

Once you install and deploy the service as explained in the [⚙️ Installation section](#-installation), you can make a `POST` to the `{{baseUrl}}/rulesets/refresh` endpoint (with this request, the application will download the new ruleset to the `/src/rules` folder, overwriting the current rules):

    curl --location --request POST 'http://localhost:8080/apifirst/v1/rulesets/refresh'
<!-- todo: update localhost URL -->

* You can change the location where the rules are downloaded modifying the configuration file:

  ```yml
  cerws:
    common:
      rest:
        client:
          github-rest-client:
            lint-repository-folder: "/src/rules"
  ```

  * If you modify this path you will need to update the following properties to update their paths too:

    ```yml
    cerws:    
      lint:    
        rest:
          general-default-ruleset: # Path of the file with the rules that will be applied in the linting of rest APIs
          security-default-ruleset: # Path of the file with the rules that will be applied in the security linting of rest APIs
        event:
          general-default-ruleset: # Path of the file with the rules that will be applied in the linting of async APIs
        avro:
          general-default-ruleset: # Path of the file with the rules that will be applied in the linting of async APIs with Avro
        grpc:      
          configuration-directory: # Path of the protolint configuration 
          severities-file:  # Path of the configuration file with the severity of the violations for grpc rules (by default the severity is warn)
      markdown:    
        markdown-lint-config: # Path of the markdownlint configuration file 
        markdown-lint-api-custom-rules: # File path with custom markdownlint rules 
    ```
* You can also use the [apicli](link) tool to update the rulesets with the [Rulesets](link) repository with a CLI command.


### Custom rules

The Certificator rules are based on different linters depending on the files that are linting. If you want to create your own rules, you should follow each linter's documentation:

- For OpenAPI/AsyncAPI, follow [Spectral documentation](https://docs.stoplight.io/docs/spectral/01baf06bdd05a-create-a-ruleset).
- For documentation files, follow [markdownlint documentation](https://github.com/DavidAnson/markdownlint/blob/main/doc/CustomRules.md).
- For gRPC, follow [protolint documentation](https://github.com/yoheimuta/protolint/tree/d19308920340bcaf80064c65d24a818d12b3fb71#creating-your-custom-rules).


## 🫂 Support 

If you have any kind of doubts regarding Certificator, don't hesitate to [contact us](email)! We are delighted to help. 


## 👏 Contributing 

You are always welcome to contribute to this project! Please have a look at the [contribution guidelines](contributing-file) before getting to code. 

(example of file: https://github.com/athityakumar/colorls/blob/main/CONTRIBUTING.md) 

## ⚖️ License 

TBD 