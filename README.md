# Olympix Tests Generation

## Overview

The Olympix Test Generation action enables Olympix's test generator tool to be incorporated into continuous integration workflows for code repositories on GitHub. The action currently performs code analysis and test generation on projects written in Solidity. By using this action, Solidity developers can focus on building their smart contracts and let us handle the test creation when the CI workflow runs.


## Features

- **Unit Tests Generation:** Automates the creation of unit tests ensuring the correctness and reliability of your code

## Getting Started

1. Add a GitHub repository secret with your Olympix API token and set an environment variable on GitHub Workflow named `OLYMPIX_API_TOKEN` with the secret you just added
2. Add the file `opix.config.json` to the root project providing info for the tests generation
3. Add the `olympix/test-generator` GitHub Action into your workflow

***Add the following steps if you want to add the generated tests directly to your repository**

4. Create a GitHub [`personal access token`](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens), add a GitHub repository secret with the personal access token you created and set an environment variable on GitHub Workflow named `OLYMPIX_GITHUB_ACCESS_TOKEN` with the secret you just added
5. Create a branch named `opix-unit-test` where the tests will be commited

## Usage

Here's a workflow example that utilizes the Olympix Tests Generation Action. In this scenario, the process to generate the tests will be triggered every time a merge from `main` branch happens to the `opix-unit-test` branch:

```shell
name: Unit Test Generation Workflow
on:
  pull_request:
    types:
      - closed
      
jobs:
  test-generation:
    if: github.event.pull_request.merged == true && github.head_ref == 'main' && github.base_ref == 'opix-unit-test'
    
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 18.17.1

      - name: Install dependencies
        run: npm install
      
      - name: Unit Test Generator
        uses: olympix/test-generator@main
        env:
          OLYMPIX_API_TOKEN: ${{ secrets.OLYMPIX_API_TOKEN }}
          OLYMPIX_GITHUB_ACCESS_TOKEN: ${{ secrets.OLYMPIX_GITHUB_TOKEN }}
```

Here's an example of `opix.config.json` file that sets two contracts to have tests generated (currently we support up to 5 contracts at a time):

```json
{
    "recipientEmail": "example@olympix.ai",
    "coverageArgs": [],
    "testArgs": [],
    "buildArgs": [],
    "opixTestDir": "test",
    "testContracts": [
      {
        "subjectContractName": "SampleCoin",
        "subjectContractPath": "contracts/SampleCoin.sol",
        "testContractPath": "test/SampleCoin.t.sol",
        "testContractName": "SampleCoinTest"
      },
      {
        "subjectContractName": "ExampleContract",
        "subjectContractPath": "contracts/ExampleContract.sol",
        "testContractPath": "test/ExampleContract.t.sol",
        "testContractName": "ExampleContractTest"
      }
    ]
}
```

The workflow will start and an email will be sent to the address set on `opix.config.json` with the result attached. If it is set to integrate with GitHub repository, it will commit the generated tests to the `opix-unit-test` branch and it will open a pull request to the `main` branch with the added tests. The generation time varies based on the size and complexity of the contracts

![unit_test_generation_successfully](https://github.com/olympix/test-generator/blob/main/img/test_generation_workflow.png)

<br/>

## Support Contact

If you have any question, feedback, or need help, feel free to contact us at contact@olympix.ai
