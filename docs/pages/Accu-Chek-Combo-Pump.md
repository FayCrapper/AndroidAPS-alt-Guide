# Accu-Chek Combo Pump

The Roche Accu-Chek Combo pump is widely available and can now be used for looping. However, due to some vagaries of the Bluetooth interface you will need to use a phone with LineageOS 14.1 in order to pair with it. If you want all the technical details why you can find them [here](https://github.com/gregorybel/combo-pairing/blob/master/README.md).

You will also need to install a second app known as [Ruffy](https://github.com/MilosKozak/ruffy). This emulates the Combo handset and issues the same commands that you would if you were using your handset.

Having done that you will need to access the pump's configuration settings and make a few simple changes to enable it to work successfully in a loop. To do this you will need some configuration software and an interface cable - both freely available from Roche.

## Hardware you will need

- A Roche Accu-Chek Combo (any firmware, they all work)
- A Smartpix or Realtyme device together with the 360 Configuration
  Software to configure the pump.
  Roche sends out Smartpix devices and the configuration software
  free of charge to their customers upon request.
- A compatible phone: An Android phone with a phone running LineageOS 14.1 (formerly CyanogenMod) or Android 8.1 (Oreo). The LineageOS 14.1 has to be a recent version from at least June 2017 since the change needed to pair the Combo pump was only introduced at that time. A list of phones can be found in the [AndroidAPS Phones](https://docs.google.com/spreadsheets/d/1gZAsN6f0gv6tkgy9EBsYl0BQNhna0RDqA9QGycAqCQc/edit#gid=698881435) document.
Please be aware that this is not complete list and reflects personal user experience. You are encouraged to also enter your experience and thereby help others (these projects are all about paying it forward).

- Be aware that while Android 8.1 allows communicating with the Combo, there are still issues with AndroidAPS on 8.1.
  For advanced users, it is possible to perform the pairing on a rooted phone and transfer it to another rooted
  phone to use with ruffy/AndroidAPS, which must also be rooted. This allows using phones with Android < 8.1 but
  has not been widely tested: https://github.com/gregorybel/combo-pairing/blob/master/README.md
Help
## Limitations

- Extended bolus and multiwave bolus are not supported (see [Extended Carbs](https://androidaps.readthedocs.io/en/latest/pages/Extended-Carbs.html) instead)
- Only one basal profile is supported.
- Setting a basal profile other than 1 on the pump, or delivering extended boluses or multiwave
  boluses from the pump interferes with TBRs and forces the loop into low-suspend only mode for 6 hours
  as the the loop can't run safely under these conditions.
- It's currently not possible to set the time and date on the pump, so daylight saving times
  changes have to be performed manually (you may disable the phone's automatic clock update in the evening and
  change it back in the morning together with the pump clock to avoid an alarm during the night).
- Currently only basal rates in the range of 0.05 to 10 U/h are supported. This also applies when modifying
  a profile, e.g. when increasing to 200%, the highest basal rate must not exceed 5 U/h since it will be
  doubled. Similarly, when reducing to 50%, the lowest basal rate must be at least 0.10 U/h.
- If the loop requests a running TBR to be cancelled the Combo will set a TBR of 90% or 110%
  for 15 minutes instead. This is because cancelling a TBR causes an alert on the pump which
  causes a lot of vibrations.
- Occasionally (every couple of days or so) AndroidAPS might fail to automatically cancel
  a TBR CANCELLED alert, which the user then needs to deal with (by pressing the refresh button in AndroidAPS
  to transfer the warning to AndroidAPS or confirming the alert on the pump).
- Bluetooth connection stability varies with different phones, causing "pump unrechable" alerts, 
  where no connection to the pump is established anymore. If that error occurs, make sure Bluetooth 
  is enabled, press the Refresh button in the Combo tab to see if this was caused by an intermitted 
  issue and if still no connection is established, reboot the phone which should usually fix this. 
  There is another issue were a restart doesn't help but a button on the pump must be pressed (which 
  resets the pump's Bluetooth), before the pump accepts connections from the phone again. There is very 
  little that can be done to remedy either of those issues at this point. So if you see those errors 
  frequently your only option at this time is to get another phone that's known to work well with 
  AndroidAPS and the Combo (see above).
- Issuing a bolus from the pump will be not always be detected in time (checked for whenever AndroidAPS connects to the pump), and might take up to 20 minutes in the worst case. Boluses on the pump are always checked before a high TBR or a bolus issued by AndroidAPS but due to the limitations AndroidAPS will then refuse to issue the TBR/Bolus as it was calculated under false premises. (-> Don't bolus from the Pump! See chapter *Usage*)
- Setting a TBR on the pump is to be avoided since the loop assumes control of TBRs. Detecting a new TBR on the pump might take up to 20 minutes and the TBR's effect will only be accounted from the moment it is detected, so in the worst case there might be 20 minutes of a TBR that is not reflected in IOB. 

## Setup

- Configure the pump using 360 config software. If you do not have the software, please contact your Accu-Chek hotline. They usually send registered users a CD with the "360° Pump Configuration Software" and a SmartPix USB-infrared connection device (the Realtyme device also works if you have that).
  - **Required changes** (marked green in screenshots):
    - Set/leave the menu configuration as "Standard", this will show only the supported
      menus/actions on the pump and hide those which are unsupported (extended/multiwave bolus,
      multiple basal rates), which cause the loop functionality to be restricted when used because
      it's not possible to run the loop in a safe manner when used.
    - Verify the _Quick Info Text_ is set to "QUICK INFO" (without the quotes, found under _Insulin Pump Options_).
    - Set TBR _Maximum Adjustment_ to 500%
    - Disable _Signal End of Temporary Basal Rate_
    - Set TBR _Duration increment_ to 15 min
    - Enable Bluetooth
  - **Recommended changes** (marked blue in screenshots)
    - Set low cartridge alarm to your liking
    - Configure a max bolus to suited yourself to protect against bugs in the software delivering over-large boluses
    - Similarly, configure maximum TBR duration as a safeguard. This needs to be at least 3 hours, because
      the pump disconnect option in AndroidAPS may need to set a 0% TBR for up to 3 hours.
    - You may want to enable the key lock on the pump to prevent bolusing from the pump. But be aware that if you do this and you become separated from the phone you use to run AndroidAPS then you won't be able to operate the pump manually. If you do bolus from the pump then AndroidAPS will not be aware of this fact until it next reads the pump history and this may be a while. 
    - Set display timeout and menu timeout to the minimum of 5.5 and 5 respectively. This allows AndroidAPS to
      recover more quickly from error situations and reduces the amount of vibration alarms that may occur.

![Screenshot of user menu settings](../images/combo/combo-menu-settings.png)

![Screenshot of TBR settings](../images/combo/combo-tbr-settings.png)

![Screenshot of bolus settings](../images/combo/combo-bolus-settings.png)

![Screenshot of insulin cartridge settings](../images/combo/combo-insulin-settings.png)

- Install AndroidAPS as described in this documentation.
- Make sure to read the wiki to understand how to setup AndroidAPS.
- Select the MDI plugin in AndroidAPS, not the Combo plugin at this point to avoid the Combo
  plugin from interfering with Ruffy during the pairing process.
- Follow the link http://ruffy.AndroidAPS.org and clone Ruffy via Git. Use the same branch as you use for
  AndroidAPS, right now that's the `combo` branch, later on there will be the regular `master` and `dev` branches.
- Install ruffy and use it to pair the pump. If it doesn't work after multiple attempts, switch to the `pairing` branch, pair the pump and then switch back the original branch.
  If the pump is already paired and can be controlled via ruffy, installing the `combo` branch is sufficient.
  Note that the pairing processing is somewhat fragile (but only has to be done once)
  and may need a few attempts; quickly acknowledge prompts and when starting over, remove the pump device
  from the Bluetooth settings beforehand. Another option to try is to go to the Bluetooth menu after
  initiating the pairing process (this keeps the phone's Bluetooth discoverable as long as the menu is displayed)
  and switch back to Ruffy after confirming the pairing on the pump, when the pump displays the authorization code.
  If you're unsuccessful in pairing the pump (say after 10 attempts), try waiting up to 10s before confirming the pairing on the pump (when the name of the phone is displayed on the pump). If you have configured the menu timeout to be 5s above, you need to increase it again. Some users reported they needed to do this.
- When AndroidAPS is using ruffy, the Ruffy app can't be used. The easiest way is to just
  reboot the phone after the pairing process and let AndroidAPS start Ruffy in the background.
- If the pump is completely new, you will need to do one bolus on the pump, so the pump creates a first history entry.
- Before enabling the Combo plugin in AndroidAPS make sure your profile is set up
  correctly and activated(!) and your basal profile is up to date as AndroidAPS will sync the basal profile
  to the pump. Then activate the Combo plugin. Press the _Refresh_ button on the Combo tab to initialize the 
  pump.
- To verify your setup, with the pump **disconnected**, use AndroidAPS to set a TBR of 500% for 15 min and issue a bolus. The pump should now have a TBR running and the bolus in the history. AndroidAPS should also show the active TBR and delivered bolus.

## Usage

- Keep in mind that this is not a product, esp. in the beginning the user needs to monitor and understand the system,
  its limitations and how it can fail. It is strongly advised NOT to use this system when the person
  using it is not able to fully understand the system.
- Read the OpenAPS documentation https://openaps.org to understand the loop algorithm AndroidAPS
  is based upon.
- Read the wiki to learn about and understand AndroidAPS http://wiki.AndroidAPS.org
- Ruffy provides the same functionality as the handset that comes with the Combo.
  The handset mirrors the pump screen and relays button presses to the pump. The Ruffy app is needed to make this functionality available to AndroidAPS. A `scripter` components reads the screen
  and automates entering boluses, TBRs and so on by simulating button presses and then checking the responses to make sure inputs are processed correctly.
  AndroidAPS then interacts with the scripter to apply loop commands and to administer boluses.
  This mode has some restrictions: it's comparatively slow (but well fast enough for what it is used for),
  and setting a TBR or giving a bolus causes the pump to vibrate.
- The integration of the Combo with AndroidAPS is designed with the assumption that all inputs are
  made via AndroidAPS. Boluses entered on the pump directly will be detected by AndroidAPS when it checks the pump history, but it may take
  up to 20 min before this happens. Reading boluses delivered directly on
  the pump is a safety feature and not meant to be regularly used (the loop requires knowledge of carbs
  consumed, which can't be entered on the pump, which is another reason why all inputs should be done
  in AndroidAPS). 
- Don't set or cancel a TBR on the pump. The loop assumes control of TBR and cannot work reliably otherwise, since it's not possible to determine the start time of a TBR that was set by the user on the pump.
- The pump's first basal rate profile is read when AndroidAPS starts and is updated by the app as necessary.
  The basal rate should not be manually changed on the pump, but will be detected and corrected as a safety
  measure (don't rely on safety measures by default, this is meant to detect an unintended change on the pump).
- It's recommended to enable key lock on the pump to prevent bolusing from the pump, esp. when the
  pump was used before and using the "quick bolus" feature was a habit.
  Also, with keylock enabled, accidentally pressing a key will NOT interrupt active communication
  between AndroidAPS and pump.
- When a BOLUS/TBR CANCELLED alert starts on the pump during bolusing or setting a TBR, this is
  caused by a disconnect between pump and phone, which happens from time to time. AndroidAPS will try to reconnect and confirm the alert
  and then retry the last action (boluses are NOT retried for safety reasons). Therefore,
  such an alarm can be ignored as AndroidAPS will confirm it automatically, usually within 30s (cancelling it is not problem, but will lead to the currently
  active action to have to wait till the pump's display turns off before it can reconnect to the
  pump). If the pump's alarm continues, automatic corfirmation failed, in which case the user
  needs to confirm the alarm manually.
- When a low cartridge or low battery alarm is raised during a bolus, they are confirmed and shown
  as a notification in AndroidAPS. If they occur while no connection is open to the pump, going to the
  Combo tab and hitting the Refresh button will take over those alerts by confirming them and
  show a notification in AndroidAPS.
- When AndroidAPS fails to confirm a TBR CANCELLED alert, or one is raised for a different reason,
  hitting Refresh in the Combo tab establishes a connection, confirms the alert and shows
  a notification for it in AndroidAPS. This can safely be done, since those alerts are benign - an
  appropriate TBR will be set again during the next loop iteration.
- For all other alerts raised by the pump: connecting to the pump will show the alert message in
  the Combo tab, e.g. "State: E4: Occlusion" as well as showing a notification on the main screen.
  An error will raise an urgent notification. AndroidAPS never confirms serious errors on the pump,
  but let's the pump vibrate and ring to make sure the user is informed of a critical situation
  that needs action.
- After pairing, ruffy should not be used directly (AndroidAPS will start in the background as needed),
  since using ruffy at AndroidAPS at the same time is not supported.
- If AndroidAPS crashes (or is stopped from the debugger) while AndroidAPS and the pump were communicating (using
  ruffy), it might be necessary to force close ruffy. Restarting AndroidAPS will start ruffy again.
  Restarting the phone is also an easy way to resolve this if you don't know how to force kill
  an app.
- Don't press any buttons on the pump while AndroidAPS communicates with the pump (Bluetooth logo is
  shown on the pump).
