# Olympix Mutation Test Generation

## Overview

The Olympix Test Generation action enables Olympix's test generator tool to be incorporated into continuous integration workflows for code repositories on GitHub. The action currently performs code analysis and test generation on projects written in Solidity. By using this action, Solidity developers can focus on building their smart contracts and let us handle the test creation when the CI workflow runs.


## Features

- **Mutation Tests Generation:** Automates the creation of unit tests ensuring the correctness and reliability of your code

## Getting Started

1. Add a GitHub repository secret with your Olympix API token and set an environment variable on GitHub Workflow named `OLYMPIX_API_TOKEN` with the secret you just added
2. Add the `olympix/mutation-test-generator` GitHub Action into your workflow

## Usage

Here is an example workflow which triggers on each commit that contains the string `OPIX-GEN-MUTATION-TESTS` and runs `forge install` and `npm install` before triggering the mutation test generator. 
Note that we need to include `-p path/to/Contract.sol` for each contract in the args passed to the test generator.

```shell
name: Mutation Test Generation Workflow
on:
  push

jobs:
  mutation-test-generation:
    
    if: contains(github.event.head_commit.message, 'OPIX-GEN-MUTATION-TESTS')
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
        with: 
          submodules: recursive


      - name: Install dependencies
        run: npm install
     
      - name: Install Foundry
        uses: foundry-rs/foundry-toolchain@v1

      - name: Run forge install 
        run: |
          forge install


      - name: Mutation Test Generator
        uses: olympix/mutation-test-generator@main
        env:
          OLYMPIX_API_TOKEN: ${{ secrets.OLYMPIX_API_TOKEN }}
          OLYMPIX_GITHUB_ACCESS_TOKEN: ${{ secrets.OLYMPIX_GITHUB_TOKEN }}
        with:
          args: -p src/subjectContract1.sol -p src/subjectContract2.sol
```


The workflow will start and an email will be sent to the address related to the api token with the result attached. The generation time varies based on the size and complexity of the contracts

![unit_test_generation_successfully](https://github.com/olympix/test-generator/blob/main/img/test_generation_workflow.png)

<br/>

## Support Contact

If you have any question, feedback, or need help, feel free to contact us at contact@olympix.ai
