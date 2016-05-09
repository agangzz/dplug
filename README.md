# dplug [![Build Status](https://travis-ci.org/p0nce/dplug.png?branch=master)](https://travis-ci.org/p0nce/dplug)

<img alt="logo" src="https://cdn.rawgit.com/p0nce/dplug/master/logo.svg" width="200">

`dplug` is a library for creating native audio plugins as simply as possible.


## Current features

- Creating VST 2.4 plugins on Windows and Mac OS X, 32-bit and 64-bit
- Creating Audio Unit v2 plugins for Mac OS X, 32-bit and 64-bit
- Comes with a few music DSP algorithms
- Comes with a suite of `tools` to make plugin creation faster (bundling, color correction, regression tests)
- Unique (bypasseable) PBR system for graphics
- Comes with a few UI widgets

![Example screenshot](screenshot.jpg "With a bit of work")


### Release notes

- v3.x.y (09/05/2016):
  * Audio Unit compatibility added, with both Cocoa and Carbon UI. What is still missing from AU: Audio Component API, sandboxing, v3. In other words it's on parity with IPlug but not JUCE.
  * The `release` tool is now much more friendly to use.
  * Special keys in `dub.json` are now expected in a `plugin.json` file next to dub.json. In the future it will be the place of autority for information about a plugin, for now this has to be duplicated in `buildPluginInfo()` override. An empty `plugin.json` is OK, defaults are in place.
  * The Wiki became a place to visit.
  * no API guarantee yet. Do not expect things not to change.

- v2.x.y:
  * this is the next release, and API will break without notice
  * supports VST for 32-bit and 64-bit, Windows and Mac
  * `release` tool now expects a VST or AU configuration, see the `distort` example for details
  * special `dub.json` key `CFBundleIdentifier` became `CFBundleIdentifierPrefix`, see how `distort` works to update your plugins dub.json
  * 10.6 compatibility dropped.

- v1.x.y:
  * initial release, VST support for 32-bit and 64-bit, Windows and Mac



## Tutorial

https://auburnsounds.com/blog/2016-02-08_Making-a-Windows-VST-plugin-with-D.html



## How to build plugins

### For Windows:
- Use DMD >= v2.067 or LDC >= v1.0.0
- Install DUB, the D package manager: http://code.dlang.org/download
- Go into an example directory
- Type `dub --compiler=dmd` or `dub --compiler=ldc2` depending on the compiler used.

### For OS X:
- Use DMD >= v2.067 or LDC >= v1.0.0
- Install DUB, the D package manager: http://code.dlang.org/download
- Build and use the `release` tool which is in the `tools/release/` directory.
- Go into an example directory
- Type `release --compiler dmd` or `release --compiler ldc` depending on the compiler used.
- This tool is needed to create the whole bundle.


## Licenses

dplug has three different licenses depending on the part you need.
For an audio plugin, you would typically need all three.
I recommend that you check individual source files for license information.

### Plugin format wrapping

Plugin wrapping is heavily inspired by the WDL library (best represented here: https://github.com/olilarkin/wdl-ol).

Some files falls under the Cockos WDL license.

Important contributors to WDL include:
- Cockos: http://www.cockos.com/
- Oliver Larkin: http://www.olilarkin.co.uk/


However dplug is **far** from a translation of WDL:




### VST SDK translation

This sub-package falls under the Steinberg VST license.

VST is a trademark of Steinberg Media Technologies GmbH.
Please register the SDK via the 3rd party developper license on Steinberg site.

Before you make VST plugins with dplug, you need to read and agree with the license for the VST3 SDK by Steinberg.
If you don't agree with the license, don't make plugins with dplug.
Find the VST3 SDK there: http://www.steinberg.net/en/company/developers.html

### Misc

Other source files fall under the Boost 1.0 license.


## FAQ

- How do I build plugins for OS X?

You need to use the `release` program in the `tools`directory.
This tool create a bundle and Universal Binaries as needed. 
Like most D programs, you can build it by typing `dub`.

- What is the oldest supported Windows version?

Windows Vista. Users report plugins made with both DMD and LDC work on Windows XP. 
But this target isn't officially supported by D compilers.

- What is the oldest supported OS X version?

OS X 10.7+
Probably possible to go below in cases, but impractical. To try that modify the "release" tool.
D compilers do not support OS X 10.6 anymore.

- What D compiler can possibly be used?

   See `.travis.yml` for supported compilers.

- Is dplug stable?

No. The interface tend to change for improvements.
Pin the version you use using DUB version specifications.


- Will you fix the bugs I encounter?

I might but don't guarantee it. **This software is released for free. It doesn't mean support is for free.** 
If you want something to be changed, you can:
- ask it
- do it yourself 
- use the bounty system


![Rendering](rendering.jpg)


## Comparison vs IPlug

  Pros:
  - No dispatcher-wide mutex lock. All locks are of a short duration, to avoid blocking the audio thread.
  - Plugin parameters implement the Observer pattern.
  - Float parameters can have user-defined mapping
  - PBR-style rendering lets you have a good visual quality with less disk space, at the cost of more work
  - No need to deal with resource compilers: D can `import("filename.ext")` them.
  - No need to maintain IDE project files, they are generated by DUB.
  - No need to make Info.plist files, they are generated instead.
  - No need to use Xcode whatsoever.

  Cons:
  - Less battle-tested in general.
  - Less man-power.
  - Hipster compilers are used, though they get better all the time.
  - API may change without notice (pin the version of dplug you use).
  - AAX and VST3 unimplemented
  - No resizeable UI yet.
  - No HDPI support yet.
  - No modal windows, and no plans for it.


## Contents of the tree

### dplug:client
  * Abstract plugin client interface. Currently implemented once for VST.

### dplug:host
  * Abstract plugin host interface.

### dplug:vst
  * VST SDK D bindings
  * VST plugin client

### dplug:au
  * Audio Unit plugin client (WIP)

### dplug:window
   * implements windowing for Win32, Cocoa and Carbon

### dplug:gui
   * Needed for plugins that do have an UI
   * Toolkit includes common widgets (knob/slider/switch/logo/label)
   * PBR-based renderer for a fully procedural UI (updates are lazy and parallel)

### dplug:dsp
  * Basic support for audio processing:
    - FFT and windowing functions (include STFT with tunable overlap and zero-phase windowing)
    - FIR, 1st order IIR filters and RBJ biquads (no higher order IIR filters)
    - mipmapped wavetables for antialiased oscillators
    - noise generation including white/pink/demo noise
    - various kinds of smoothers and envelopes
    - delay-line and interpolation

### Examples
   * `examples/distort`: mandatory distortion plugin
   * `examples/ms-encode`: simplest plugin for tutorial purpose
   * `examples/time_stretch`: resampling x2 through FFT zero-padding

### Tools
   * `tools/pbr-sketch`: playground for creating plugin background textures
   * `tools/release`: DUB frontend to build Mac bundles and use LDC with proper envvars
   * `tools/process`: plugin host for testing audio processing speed/reproducibility
   * `tools/wav-compare`: comparison of WAV files
   * `tools/lift-gamma-gain`: tool to adjust color correction curves on a finished UI
