The Bendix/King KAP 140 (version 1.2)
-------------------------------------

This implementation is build according to the user manual.
You can set the desired model in the kap140-config.xml (comments are there).
If your aircraft has a encoding altimeter, point the config to the correct properties and
set 'baro-tied' to true.
If not, set up a extra altimeter in your /sim/instrumentation/, config the properties
to the extra altimeter and set 'baro-tied' to false.
Configure the nav-selector in nav-selector-config.xml.
This enables you to use up to three navigation sources (nav radios, rnav devices, GPS, etc...).
You can bind buttons or a switch to change '/instrumentation/nav-source/selector' to 0, 1 or 2.
If you only have one source, configure only the first and avoid a switch or button in the cockpit.
The nav-selector is only a bunch of filters to push all information to one location.
The AP navigate according to the information under '/instrumentation/nav-source'.
From the AP point of view it doesnt matter where the information came from.

If the KAP140 isn't tied with a altimeter, the standard pressure will flash after Pre Flight Test (PFT) is finished.
As long as the standard pressure is flashing, the autopilot can't be activated!
The pilot must set the desired pressure (with the knobs) or confirm the standard pressure (press BARO button).

If your aircraft has a HSI installed, set 'hsi-installed' to true.
If not, there is no radial pointer, therefor the KAP140 uses the heading bug for the radial.
In this situation, if you switch from ROL or HDG to NAV, APR or REV, you will see HDG flashes
in the display for 5 seconds as a reminder for the pilot to set the heading bug to the desired radial.
After 5 seconds (blinking HDG stops), the KAP140 take the heading bug as radial.

To activate the KAP140, press and hold the AP button for at least 0.25 seconds.

Engaging the NAV, APR or REV mode happens if the aircraft is close enough.
This means, the deviation needle is only 3 points left or right of the center (or closer).
The actual scaling of the points doesn't matter. Normally, on VOR one point represent 2 degrees and
on LOCalizer (ILS) one point represent 0.8 degrees. Often on RNAV one point represent 1 NM.
But the KAP140 doesn't know the scaling. It catch inside 3 point left or right.

If the KAP140 is in NAV, APR or REV mode and the deviation needle move more then 3 points away,
the mode starts flashing. The autopilot will try to come back on the right track.
If the autopilot isn't back in the next 30 seconds, it automatically disengage.

If the autopilot is in VS mode, one press on UP or DOWN button change the display to show the actual FPM without altering.
Every press on the UP or DOWN button while the FPM is shown in the display, will alter the rate by 100fpm.
If you press and hold the UP or DOWN button, the rate will be changed by around 300fpm per second.

If the autopilot is in ALT mode, the UP and DOWN button set the holding altitude up or down by around 20ft.
If you press and hold the UP or DOWN button, the autopilot command a climb/descend with 500fpm.
Immediately as you release the button, the autopilot catch the new pressure altitude.

The 'kap140-autopilot.xml' is completely reworked by Joshua Davidson aka Octal450 (big thanks for this great control loops).


Include the KAP 140 in your aircraft:
-------------------------------------

- copy the 'kap140' directory inside your aircraft tree
    (as example to 'Models/Interior/Instruments/')

- rename one of the texture files to 'kap140.png' (actually there is a dark and a bright texture)

- add the 'kap140.xml' to your panel (like every other model)
    you can set some effects in the file 'kap140.xml'
    there is a group 'annunciators' to change the display brightness
    there is also a group 'buttons' for animating avionic lights
    Important! there are two objects ('glass' and 'base-sensor')
    make sure this two objects have a transparent effect!

- if your aircraft hasn't a encoding altimeter, add a 'altimeter' in your instrumentation
    (under <sim> … <instrumentation>)

- open the file 'kap140-config.xml' and configure your setup

- open the file 'nav-selector-config.xml' and configure your setup

- add the file 'nav-selector.xml' as autopilot under '<sim> … <systems>' in your set-file
    as example:

        <autopilot n="3">
            <name>NAV-selector</name>
            <path>Models/Interior/Instruments/kap140/nav-selector.xml</path>
        </autopilot>

- add the file 'kap140-autopilot.xml' as autopilot under '<sim> … <systems>' in your set-file
    as example:

        <autopilot n="4">
            <name>KAP140-autopilot</name>
            <path>Systems/kap140-autopilot.xml</path>
        </autopilot>

- add the file 'kap140-proprules.xml' as property-rule under '<sim> … <systems>' in your set-file
    as example:

        <property-rule n="100">
            <name>KAP140-control</name>
            <path>Models/Interior/Instruments/kap140/kap140-proprules.xml</path>
        </property-rule>

- include the file 'kap140-properties.xml' under '<sim> … <autopilot>' in your set-file
    as example if you dont have a autopilot section:

        <autopilot include="Models/Interior/Instruments/kap140/kap140-properties.xml"/>

    if you already have a autopilot section, add it this way:

        <autopilot include="Models/Interior/Instruments/kap140/kap140-properties.xml">
            …
        <autopilot/>

Important: the files 'kap140-autopilot.xml', 'kap140-proprules.xml' and 'kap140.xml' have a 'include' clause!
make sure that this files can find 'kap140-config.xml' (change the 'include' clause if necessary).

- move the sound files (kap140-alert.wav and kap140-disengage.wav) in your sound directory

- add the following lines in your sound.xml (don't forget to set the position correctly):

<!-- kap140-sound end -->
    <kap140-disengage>
      <name>KAP140 disengage</name>
      <path>kap140-disengage.wav</path>
      <condition>
        <or>
          <equals>
            <property>autopilot/kap140/panel/state</property>
            <value>3</value>
          </equals>
          <property>autopilot/kap140/panel/ap-timer</property>
        </or>
      </condition>
      <volume><factor>0.2</factor></volume>
      <position>
        <x>-0.444</x>
        <y> 0.229</y>
        <z> 0.312</z>
      </position>
    </kap140-disengage>

    <kap140-alert>
      <name>KAP140 altitude alerter</name>
      <path>kap140-alert.wav</path>
      <condition>
        <property>autopilot/kap140/panel/alt-alert-sound</property>
      </condition>
      <volume><factor>0.2</factor></volume>
      <position>
        <x>-0.444</x>
        <y> 0.229</y>
        <z> 0.312</z>
      </position>
    </kap140-alert>
<!-- kap140-sound end -->



- move the file 'kap140-dlg.xml' in to '/gui/dialogs/'
    If you dont have this directories, create it.
    Dialogs must be in this directory, otherwise FG can not find it.



- include the file 'nav-selector-properties.xml' under '<sim> … <instrumentation>' in your set-file
    as example if you dont have a 'instrumentation' section:

        <instrumentation include="Models/Interior/Instruments/kap140/nav-selector-properties.xml"/>

    if you already have a 'instrumentation' section, add it this way:

        <instrumentation include="Models/Interior/Instruments/kap140/nav-selector-properties.xml">
            …
        <instrumentation/>



You can disengage the KAP140 by keypress (AP disconnect).

<!-- KAP140 disconnect begin -->
  <key n="100"> <!-- set a proper key -->
    <desc>KAP140 disconnect</desc>
    <binding>
      <command>property-assign</command>
      <property>autopilot/kap140/settings/ap-disc</property>
      <value>1</value>
    </binding>
  </key>
<!-- KAP140 disconnect end -->



The KAP140 support CWS (Control Wheel Steering).
To use this feature, add the following lines to your keyboard.xml:

<!-- CWS begin -->
  <key n="99"> <!-- set a proper key -->
    <desc>Control Wheel Steering (CWS)</desc>
    <binding>
      <command>property-assign</command>
      <property>autopilot/kap140/settings/cws</property>
      <value>1</value>
    </binding>
    <mod-up>
      <binding>
        <command>property-assign</command>
        <property>autopilot/kap140/settings/cws</property>
        <value>0</value>
      </binding>
    </mod-up>
  </key>
<!-- CWS end -->



Finish!
Happy flying :)
Sascha Reißner
2021
