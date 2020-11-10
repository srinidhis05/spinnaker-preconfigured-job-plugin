![CI](https://github.com/mayadata-io/spinnaker-preconfigured-job-plugin/workflows/CI/badge.svg)
![Latest Kork](https://github.com/spinnaker-plugin-examples/preconfiguredJob/workflows/Latest%20Kork/badge.svg?branch=master)
![Latest Orca](https://github.com/spinnaker-plugin-examples/preconfiguredJob/workflows/Latest%20Orca/badge.svg?branch=master)

This plugin demonstrates how to setup a preconfigured litmuschaos experiment job on Kubernetes.

# Usage

- Add LitmusChaos to the spinnaker plugin list by modifying the halyard config as provided below. Refer: [plugin-v2-configuration](https://spinnaker.io/guides/user/plugins/#plugin-v2-configuration-changes)

```
  spinnaker:
    extensibility:
      plugins:
        Litmuschaos.PreconfiguredJobPlugin:
          id: Litmuschaos.PreconfiguredJobPlugin
          enabled: true
          version: 0.0.3
          extensions: {}
      repositories:
        spinnaker-plugins-repository:
          id: spinnaker-plugins-repository
          url: https://raw.githubusercontent.com/mayadata-io/spinnaker-plugins-repository/main/plugins.json
```

Note: The plugin metadata is maintained in the [litmus spinnaker plugins repository](https://github.com/mayadata-io/spinnaker-plugins-repository)

- Select the LitmusChaos predefined stage in the Spinnaker pipeline, with the right "dependent stage"

  ![spinnaker-1](https://user-images.githubusercontent.com/21166217/98707580-33785700-23a6-11eb-9824-7cfe3f48730f.png)

- Configure the stage as needed, by selecting the appropriate experiment name & associated chaos properties: 

  ![spinnaker-2](https://user-images.githubusercontent.com/21166217/98707590-34a98400-23a6-11eb-826a-566d9e1e5457.png)

**Note:**

  - Ensure that the chosen experiment names are from amongst the pod-level generic chaos experiments on the [chaoshub](https://hub.litmuschaos.io)
   
    ![chaoshub-1](https://user-images.githubusercontent.com/21166217/98708155-e0eb6a80-23a6-11eb-8acc-b7c71b5c94ef.png)

  - Provide the appropriate container runtime (supported: docker, containerd, crio) 

  - Refer to the [litmus documentation](https://docs.litmuschaos.io) for more information on the experiment behaviour

  - Use the litmus install/uninstall flags set to `true` to specify if these steps are needed in the same stage. If however, you would like 
    to verify the ChaosResult CRs generated during the experiment or wish to string together more than one experiment as part of the
    pipeline, you can can use the individual [install/uninstall custom jobs](https://github.com/mayadata-io/spinnaker-chaos-templates/tree/main/templates/litmus-infra-ops) that can be updated in the orca-local.yaml

# Debugging

To debug the `litmuschaos-orca`  server component inside a Spinnaker service (like Orca) using IntelliJ Idea follow these steps:

1) Run `./gradlew releaseBundle` in the plugin project.
2) Copy the generated `.plugin-ref` file under `build` in the plugin project submodule for the service to the `plugins` directory under root in the Spinnaker service that will use the plugin .
3) Link the plugin project to the service project in IntelliJ (from the service project use the `+` button in the Gradle tab and select the plugin build.gradle).
4) Configure the Spinnaker service the same way specified above.
5) Create a new IntelliJ run configuration for the service that has the VM option `-Dpf4j.mode=development` and does a `Build Project` before launch.
6) Debug away...
