Day 2:

  Attempted local run of Nvidia "Kit" without realizing bottleneck of my lukewarm i5-1300 currently in use. I knew I was skating on thin ice for a while with the 4090RTX throttling those cores to the max with CUDA interations I5<-->4090RTX. Sure enough, Isaac Sim and Kit failed upon boot. 
Checking the boot code shows the following:

<code>|---------------------------------------------------------------------------------------------|
| Driver Version: 560.35.05     | Graphics API: Vulkan
|=============================================================================================|
| GPU | Name                             | Active | LDA | GPU Memory | Vendor-ID | LUID       |
|     |                                  |        |     |            | Device-ID | UUID       |
|     |                                  |        |     |            | Bus-ID    |            |
|---------------------------------------------------------------------------------------------|
| 0   | NVIDIA GeForce RTX 4090          | Yes: 0 |     | 24810   MB | 10de      | 0          |
|     |                                  |        |     |            | 2684      | 614effd7.. |
|     |                                  |        |     |            | 1         |            |
|=============================================================================================|
| OS: 24.04.1 LTS (Noble Numbat) ubuntu, Version: 24.04.1, Kernel: 6.8.0-51-generic
| XServer Vendor: The X.Org Foundation, XServer Version: 12101011 (1.21.1.11)
| Processor: Intel(R) Core(TM) i5-10400F CPU @ 2.90GHz | Cores: 6 | Logical: 12
|---------------------------------------------------------------------------------------------|
| Total Memory (MB): 32032 | Free Memory: 27714
| Total Page/Swap (MB): 8191 | Free Page/Swap: 8191
|---------------------------------------------------------------------------------------------|
[1.645s] [ext: omni.kit.pipapi-0.0.0] startup
[1.647s] [ext: omni.kit.pip_archive-0.0.0] startup
[1.647s] [ext: omni.kit.telemetry-0.5.0] startup
[1.670s] [ext: omni.kit.loop-default-0.2.0] startup
[1.671s] [ext: omni.kit.test-0.0.0] startup
[1.755s] [ext: omni.mtlx-0.1.0] startup
[1.755s] [ext: omni.usd.config-1.0.3] startup
[1.763s] [ext: omni.gpucompute.plugins-0.0.0] startup
[1.765s] [ext: omni.usd.libs-1.0.0] startup
[1.960s] [ext: omni.appwindow-1.1.5] startup
[2.687s] [ext: omni.kit.renderer.core-0.0.0] startup
[6.894s] [ext: omni.kit.renderer.capture-0.0.0] startup
[6.899s] [ext: omni.kit.renderer.imgui-0.0.0] startup
[7.009s] [ext: carb.audio-0.1.0] startup
[7.014s] [ext: omni.ui-2.18.5] startup
[7.034s] [ext: omni.uiaudio-1.0.0] startup
[7.036s] [ext: omni.kit.mainwindow-1.0.1] startup
[7.038s] [ext: omni.kit.uiapp-0.0.0] startup
[7.038s] [ext: omni.usd.schema.anim-0.0.0] startup
[7.242s] [ext: omni.usd.schema.geospatial-0.0.0] startup
[7.247s] [ext: omni.usd.schema.physics-0.0.0] startup
[7.275s] [ext: omni.usd.schema.omnigraph-1.0.0] startup
[7.284s] [ext: omni.usd.schema.audio-0.0.0] startup</code>

Much more GPU-related code shows as succesfull regarding omni.usd packages but is a bit redundant. 

We can see that the GPU inits as needed and the software UI boots no problem!....for about .3 seconds. Installing lighting shaders (at least that's what shown on UI) causes complete crap-out. I'm talking reboot-the-PC crap out. 
We start to jitter out when we hit the CPU portions of CUDA exchange here:

<code>2024-12-20 04:49:16 [9,074ms] [Warning] [omni.kit.hotkeys.core.registry] [Hotkey] Cannot register Global [omni.kit.widget.toolbar] omni.kit.widget.toolbar::toolbar::play.SPACE, error code: Duplicated action definition
2024-12-20 04:49:16 [9,115ms] [Warning] [carb.audio.device] audio device is misconfigured or broken {deviceIndex = 0, name = 'default'} (The device is likely misconfigured, check your $HOME/.asoundrc)
2024-12-20 04:49:16 [9,115ms] [Warning] [carb.audio.output] failed to retrieve the capabilities for device 0 {result = eDeviceLost (2)}
2024-12-20 04:49:16 [9,115ms] [Warning] [carb.audio.context] failed to set the requested output during context creation.  Using a null streamer instead {result = eDeviceLost (2)}
2024-12-20 04:49:16 [9,142ms] [Warning] [omni.hydratexture.plugin] rtx::raytracing::RaycastQueryManager is not available
2024-12-20 04:49:16 [9,155ms] [Warning] [carb.tasking.plugin] carb.tasking:pinToCurrentThread(): Task 4294967699 pinned to current thread. Thread pinning is inefficient and should be avoided!
^Z
[1]+  Stopped                 sudo ./my_name.my_app.sh --allow-root</code>



These errors indicate thread compiling issues:

  a. audio device --> (maybe user error from the hint: <code> The device is likely misconfigured, check your $HOME/.asoundrc</code> \
  b. Something is wonky with ray tracing....I'll have to look into this. Possibly Vulkan related?: <code>rtx::raytracing::RaycastQueryManager is not available</code>
  c. I think we have the real issue here --> <code>Task 4294967699 pinned to current thread. Thread pinning is inefficient and should be avoided!</code>


Proposed solution:

Buy an AMD Ryzen 7 98003d and upgrade motherboard accordingly to move forward. I have a suspiscion CUDA audio and certain unkown elements via Vulkan RayTracing settings are making Nvidia Kit/Isaac Sim UI bonk. 


We didn't get this far just to use the streaming client!!!!
