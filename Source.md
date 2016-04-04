Our project sources are hosted at http://gitorious.org/rowboat



## Host Setup to download the sources ##

Before you download the source, you will need [git](http://git-scm.com/) and [repo](http://source.android.com/source/git-repo.html) tools.

On an Ubuntu 8.04 host, you can do the following:

Getting git:
```
$ sudo apt-get install git-core
```

Getting repo:
```
$ curl http://android.git.kernel.org/repo >~/bin/repo
$ chmod a+x ~/bin/repo
```

You can find detailed repo installing instructions [here](http://source.android.com/source/git-repo.html)

## To download the rowboat source code ##

Kindly follow the below instructions to download the sources

```
$ mkdir ~/rowboat-android
$ cd ~/rowboat-android
$ repo init -u git://gitorious.org/rowboat/manifest.git -m ManifestName
$ repo sync
```

Where _ManifestName_ is:
  * _rowboat-donut.xml_ - for donut version of rowboat
  * _rowboat-donut-dsp.xml_ - for donut version of rowboat with DSP support
  * _rowboat-eclair.xml_ - for eclair version of rowboat
  * _rowboat-eclair-dsp.xml_ - for eclair version of rowboat with DSP support
  * _rowboat-froyo.xml_ - for froyo version of rowboat
  * _rowboat-froyo-dsp.xml_ - for froyo version of rowboat with DSP support
  * _rowboat-gingerbread.xml_ - for gingerbread version of rowboat
  * _rowboat-gingerbread-dsp.xml_ - for gingerbread version of rowboat with DSP support


## ~~To download TI's Android SGX SDK~~ ##
**NOTE**: This step is no longer needed since Froyo.

Kindly follow the below steps to download the TI's Android SGX SDK release, the release works with the rowboat eclair source downloaded by following the instructions above. (Now for Eclair and Froyo versions TI's Android SGX SDK downloads with other rowboat sources to external/ti\_android\_sgx\_sdk folder.)

```
$ cd rowboat-android
$ git clone git://gitorious.org/rowboat/ti_android_sgx_sdk.git
```


## To build the core rowboat Android sources ##

To Configure and Build the sources for a OMAP35x (without DSP codecs), AM35x, AM37x based platform kindly follow the [Configure and Build wiki page](http://code.google.com/p/rowboat/wiki/ConfigureAndBuild).


## To enable DSP audio/video acceleration in OpenCore ##

If you are using a device with a DSP (i.e. OMAP3530, DM37x) then you can take advantage of the powerful multimedia acceleration capabilities of the device by following these instructions: [Enabling Multimedia Acceleration.](http://code.google.com/p/rowboat/wiki/DSP#Building_and_Testing_DSP_stack)