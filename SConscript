import os
import platform
Import('env')

android_home = env.get('ANDROID_HOME')

ANDROID_TARGET_ARCH = env.get('TARGET_ARCH')
if env.get('RELEASE'):
	ANDROID_RELEASE="release"
else:
	ANDROID_RELEASE="debug"
ANDROID_SECURED = env.get('SECURED')

os.environ['ANDROID_HOME'] = env.get('ANDROID_HOME')
os.environ['ANDROID_NDK_HOME'] = env.get('ANDROID_NDK')

if not os.path.exists(android_home + '/platforms/android-21') or not os.path.exists(android_home + '/build-tools/20.0.0'):
    print '''
***************************************** Info ********************************
*   Either 'Android API 21' is not installed or 'Android SDK Build Tools      *
*   20.0.0' is not installed. The Android SDK Manager will now open. Please   *
*   be sure to deselect all options, then select the following 2 packages:    *
*       1. Under "Tools" select "Android SDK Build-tools" Revision 20.        *
*       2. Under "Android 5.0.1 (API 21)" select "SDK Platform"               *
*       3. Continue by selecting "Install 2 Packages"                         *
*                                                                             *
*   NOTE: If you have an http proxy, please press ctrl+c now and edit/create  *
*         the following file in your $HOME directory as follows:              *
*                                                                             *
* Edit/Create file: "$HOME/.android/androidtool.cfg"                          *
*                                                                             *
*    http.proxyPort=<YOUR_PORT_NUMBER>                                        *
*    sdkman.monitor.density=108                                               *
*    http.proxyHost=<YOUR_HTTP_PROXY_ADDRESS>                                 *
*    sdkman.show.update.only=true                                             *
*    sdkman.ask.adb.restart=false                                             *
*    sdkman.force.http=true                                                   *
*    sdkman.show.updateonly=true                                              *
*                                                                             *
*******************************************************************************

...Opening Android SDK Manager now. Once you are finished, the build will continue.
'''
    os.system(android_home + '/tools/android')

def ensure_libs(target, source, env):
    return target, [source, env.get('BUILD_DIR') + 'liboc.so', env.get('BUILD_DIR') + 'liboc_logger.so']

# build android_api
jdk_env = Environment(ENV=os.environ)
jdk_env['BUILDERS']['Gradle'] = Builder(action = env.get('ANDROID_GRADLE') + ' build -b' + os.getcwd()+'/build.gradle -PTARGET_ARCH=%s -PRELEASE=%s -PSECURED=%s --stacktrace' %(ANDROID_TARGET_ARCH, ANDROID_RELEASE, ANDROID_SECURED), emitter = ensure_libs)
jdk_env['BUILD_DIR'] = env.get('BUILD_DIR')
cmdBuildApi=jdk_env.Gradle(target="base/objs", source="base/src/main/java/org/iotivity/base/OcResource.java")

jdk_env['BUILDERS']['Gradle'] = Builder(action = env.get('ANDROID_GRADLE') + ' build -b' + 'android/examples/build.gradle -PTARGET_ARCH=%s -PRELEASE=%s -PSECURED=%s --stacktrace' %(ANDROID_TARGET_ARCH, ANDROID_RELEASE, ANDROID_SECURED))
cmdBuildExamples=jdk_env.Gradle(target="../examples/devicediscoveryclient/apk", source="../examples/devicediscoveryclient/src/main/java/org/iotivity/base/examples/DeviceDiscoveryClient.java")

# android examples require android api to be built before being invoked
Depends(cmdBuildExamples, cmdBuildApi)

