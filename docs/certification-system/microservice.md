---
layout: default
title: Microservice
parent: Certification System
nav_order: 1
---

![](../../assets/images/cert-image.jpg)

<div align="center">
    <img src="../../../assets/images/cert-image.jpg" width="100px"> 
</div>

# Certification service

## Overview

<!-- todo: update -->

- [📜 Summary](#-summary)
- [⚙️ Installation](#%EF%B8%8F-installation)
- [🧑‍🏭 Performance and configuration](#️-performance-and-configuration)
- [🫂 Support](#-support)
- [👏 Contributing](#-contributing)
- [⚖️ License](#️%EF%B8%8F-license)
<!-- - [📖 Documentation](#-documentation) -->


## 📜 Summary

API Certification is the microservice responsible for getting a grade for each API. It has several modules to certify different aspects of each API and calculate a final score for them using a weighted average.

This open-source API-First-based certification service evaluates your APIs according to a set of rules that the user can customize.

This service is the heart of a certification system, compounded by the following: 

- Certificator - This service. 
- [Rulesets](link) - A compound of rules that can replace and extend the default rules. 
- [apicli](link) - The CLI that interacts with it.
- [API hub](link) - The tools that help you design your APIs.

> *NOTE: The microservice has a set of rules that are in the system **by default**. In addition, you can sync it with the [rulesets repository](link) to override the service default rules with a more-complete set.*

We want to make it simple. In the end, each certified API will be broken down into a single grade, which will mean how well-design your API is. 

If you want to know if your API complies with your design rules, if it addresses some of the OWASP vulnerabilities, and if it complies with documentation guidelines… this is your service.

> _NOTE: Due to an [issue in protolint](https://github.com/yoheimuta/protolint/issues/144), the certification of gRPC APIs in Windows it is not possible_.

## ⚙️ Installation

Know that we recommend the use of [Node v18.13.0](https://nodejs.org/es/blog/release/v18.13.0/) to work on this project. 

Deploy the service following these steps: 

1. Clone the repository:

    `git clone git@github.com:inditex/mic-openapicertification.git`

2. Place yourself in it: 

    `cd mic-openapicertification/packages` 

3. Install the dependencies: 

    `npm install`

4. Optionally, add your GitHub credentials one of the following ways to be able to validate private repositories:

   - as environment variables:

         CERWS_GH_USERNAME (GitHub username)
         CERWS_GH_PASSWORD (GitHub personal access token)
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

    `npm start` 


You can make use of this service by just making a request to its [API](Link-a-la-API-en-el-repo) or, even better, using the [apicli](Link-al-repo) CLI tool that we have developed for this matter. 

You can also use the [API hub](link) set of tools from your IDE to help you design your API at the same time you validate it with the service. The API hub provides the rating of the modules evaluated on the [certification service](link), giving you real-time insights into what score to expect.

Anyhow, let's talk about the available API endpoints (they are all documented in the API, though:cowboy_hat_face:) 
<!-- todo: link a Swagger. -->

- `[POST] /rulesets/refresh`: This operation overrides the default ruleset with a custom one with the same structure.
  
  E.g.:

  ```
  curl --location --request POST 'http://localhost:8080/apifirst/v1/rulesets/refresh'
  ```
- `[POST] /fileverify`: This verifies the retrieved file's content as long as it complies with the OpenAPI specification. 

  E.g.:

  ```
  curl --location --request POST 'http://localhost:8080/apifirst/v1/file-verify' \--header 'Content-Type: multipart/form-data' \--header 'Accept: application/json' \--form 'url="https://raw.githubusercontent.com/..."' \--form 'apiProtocol="1"'
  ```

- `[POST] /validations`: This operation creates a validation result from a ZIP file (which should contain a repository) or from a repository URL. 

  E.g.:

  ```
  curl --location --request POST 'http://localhost:8080/apifirst/v1/validations' \--header 'Content-Type: multipart/form-data' \--header 'Accept: application/json' \--form 'url="https://github.com/..."' \--form 'validationType="1"' \--form 'isVerbose="false"'
  ```

  > Take into account that you need the following repository structure to use the `validations` request with a ZIP:
  > 
  > ```
  > ├── metadata.yml
  > └── myApis
  >     └── samples
  >         ├── asyncapi-streams
  >         │   ├── asyncapi.yml
  >         │   ├── cart_lines_operations.avsc
  >         │   └── lines_operations_event.avsc
  >         └── rest
  >             └── openapi-rest.yml
  > ```
  > 
  > Being the `metadata.yml` a file that contains all APIs defined in the repo:
  > 
  > ```yml
  > apis:
  >  - name: # The API name
  >    api-spec-type:   # API type: grpc, event, rest
  >    definition-path: # Path to API folder
  >    definition-file: # API definition file
  > ```
  > 
  > For example:
  > 
  > ```yml
  > apis:
  >   - name: "REST Sample"
  >     api-spec-type: rest
  >     definition-path: myApis/samples/rest
  >     definition-file: openapi-rest.yml
  >   - name: "AsyncAPI Streams Sample"
  >     api-spec-type: event
  >     definition-path: myApis/samples/asyncapi-streams
  >     definition-file: asyncapi.yml
  > ```

## 🧑‍🏭 Performance and configuration

Now that you can ZIP a working directory, send it to the Certificator, and get a grade, you are probably wondering... How? 

As you have previously read, the microservice works with a compound of rules. It can be the default one in this current repository, or the ones in the [Rulesets repository](link).

<details>
<summary>List of default rules in the microservice</summary>


#### AsyncAPI
`itx-spectral.yaml`

<div class="markdown-preview-view">

| Rule | Description |
| --	| -- |
| contact-email | Definition must have a contact email |
| contact-url | Contact email should be a valid URI |
| mandatory-description | Description must be present |
| must-use-semantic-versioning | Version must be in semver |

</div>

---

#### Avro
`itx-spectral.yaml`

| Rule | Description |
| --	| -- |
| field-name-snake_case | All field names must match snake case pattern (e.g. snake_case) |
| fields-doc | Definition `doc` must be present and non-empty string in all fields |
| global-doc | Definition `doc` must be present and non-empty string in all types |
| type-name-pascal-case | All type names must match PascalCase pattern |

---


#### Documentation

`/markdownlint/linting-rules/api-rules/`

| Rule | Description |
| --	| -- |
| api-mandatory-about |	About section is mandatory|

---


#### OpenAPI - REST

`itx-spectral.yaml`

| Rule | Description |
| --	| -- |
| contact-email | Definition must have a contact email |
| contact-url	| Contact email should be a valid URI |
| ensure-operations-summary | Put a summary in all operations |
| openapi-version | OpenAPI version cannot be 3.1.X for the time being |

<br>

`security-spectral.yaml`

| Rule | Description |
| --	| -- |
| array-required-properties | Array size should be limited to mitigate resource exhaustion attacks. This can be done using `maxItems` |

<br>

`rulesets/api-versions.yaml`

| Rule | Description |
| --	| -- |
|must-use-semantic-versioning | All the API will be versioned following the Semantic Versioning definition |

<br>

`rulesets/examples.yaml`

| Rule | Description |
| --	| -- |
| components-param-examples | Componente parameters must have examples |
| ensure-properties-examples | Properties must have examples, description and type |
| paths-param-examples | Parameters must have examples |

<br>

`http-status-code.yaml`

| Rule | Description |
| --	| -- |
| delete-http-status-codes-resource | Each endpoint need to have defined the error codes |
| error-response-definitions | Define errors in your API following our version of the Problem Details RFC7807 |
| error-response-definitions-rfc7807-status	| Define errors in your API following our version of the Problem Details RFC7807 |
| get-http-status-codes-collections | Each endpoint need to have defined the error codes |
| get-http-status-codes-resource | Each endpoint need to have defined the error codes |
| patch-http-status-codes-resource | Each endpoint need to have defined the error codes	 |
| post-http-status-codes-collections | Each endpoint need to have defined the error codes |
| post-http-status-codes-controller | Each endpoint need to have defined the error codes |
| post-http-status-codes-resource | Each endpoint need to have defined the error codes |
| put-http-status-codes-resource | Each endpoint need to have defined the error codes |
| standard-http-status-codes | MUST use standard HTTP status codes |
| well-understood-http-status-codes	 | HTTP response codes cannot be used on all HTTP verbs |

<br>

`path.yaml`

| Rule | Description |
| --	| -- |
| path-parameters-camel-case | Path parameters should be camelCase |
| paths-no-underscore| Paths should not contain underscores |
| paths-uppercase	| Paths should not be in uppercase |
| query-camel-case| Query parameters should be camelCase |

<br>

`properties.yaml`

| Rule | Description |
| --	| -- |
| camel-case-for-properties | Use camelCase naming for attributes in request/responses and definitions |
| dto-schema-name | You SHOULD avoid ending your schemas (..schemas[*]~) with suffixes like {dto,DTO,Dto} |

<br>

`security.yaml`

| Rule | Description |
| --	| -- |
| allowed-auth-methods | The API operation uses HTTP authentication method that is not included in IANA Authentication Scheme Registry |
| allowed-verbs | Use always HTTP verbs to refer to actions in urls |
| array-required-properties | Array size should be limited to mitigate resource exhaustion attacks. This can be done using `maxItems` |
| empty-schema | The schema is empty. This means that your API accepts any JSON values. Or payload does not have any properties defined |
| empty-schema-headers | Define schemas for all header objects to restrict what input or output is allowed |
| ensure-auth | Authentication SHOULD support Basic and Bearer type |
| ensure-security-schemes | The security field of your API contract does not list any security schemes to be applied |
| global-security | Use the security field on the global level to set the default authentication requirements for the whole API |
| implicit-grant-oauth2 | Do not use implicit grant flow in OAuth2 authentication |
| negotiate-auth | Do not use the security scheme negotiateAuth |
| no-additional-properties-defined	 | While forbidding additionalProperties can create rigidity and hinder the evolution of an API - eg making it hard to accept new parameters or fields | it is possible that this flexibility can be used to bypass the schema validator and force the application to process unwanted information |
| numeric-required-properties-max-min	 | Numeric values should be limited in size to mitigate resource exhaustion |
| oauth1-auth | One or more global security schemes in your API allows using OAuth 1.0 authentication |
| resource-owner-password-auth | The API operation uses resource owner password grant flow in OAuth2 authentication |
| response-schema-defined | You have not defined any schemas for responses that should contain a body. Only acceptable if the returned HTTP status code is 204 or 304 (no content allowed for these codes), see RFC 7231 and RFC 7232 |
| schema-mandatory-parameters | One or more parameters in your API do not have schemas defined. All parameters must have either schema or content defined to restrict what content your API accepts|
| security-empty | One or more of the objects defined in the global security field contain an empty security requirement |
| security-scopes-defined | OAuth2 security requirement requires a scope not declared in the referenced security scheme |
| server-https | Set all server objects to support HTTPS only so that all traffic is encrypted |
| string-parameters-required-maxLength | String should be limited and with a maxLength to avoid out of format inputs by attackers |
| string-properties-required-maxLength | String should be limited and with a maxLength to avoid out of format inputs by attackers |



---
												
#### Proto

| Rule | Description |
| --	| -- |												
| response-schema-not-defined | You have not defined any schemas for responses that should contain a body. Only acceptable if the returned HTTP status code is 204 or 304 (no content allowed for these codes), see RFC 7231 and RFC 7232 |

</details>

<br>

If an API doesn't break any rule, it gets an A+. If there are rules broken, this grade goes down accordingly. To calculate so, we have divided the scoring system into three modules (Design, Security, and Documentation) that assign grades on a letter scale from D to A<sup>+</sup>.

<div align="center">

| Score | Letter |
|:---:|:---:|
|100|A<sup>+</sup>|
|90 - 99|A|
|75 - 89|B|
|50 - 74|C|
|0 - 49|D|

</div>

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