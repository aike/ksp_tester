
                             Soundfrost KSP Tester

1. INTRODUCTION
KSP Tester is a simple testing framework for KONTAKT scripting language KSP.
It provides repeatable testing environment for all KSP script developers.


2. DOWNLOAD
http://github.com/aike/ksp_tester


3. DEMO MOVIE
http://www.youtube.com/watch?v=iFB9NiFxebk&fmt=22


4. FEATURES
KSP Tester supports the following test:
  - note on/off test
      note transpose, note signal ignoreing, key switch behavior
  - velocity test
      velocity layer, velocity changing
  - group test
      round robin, layer range and key range of group
  - engine parameter test
      volume, pan, effect, modulation, etc...
  - midi CC test
      CC controled parameter test
  - GUI test
      You can test GUI value with PGS helper function.
  NOTE: KSP Tester does not support sound level test. 
        (e.g. waveform, spectrum analysis)

Open source
  KSP Tester is licensed under the FreeBSD License.


5. SAMPLE SCRIPT
The following is a sample test script. It plays all notes (note number 0-127) 
and it validates that the notes be played correctly.
------------------------------------
import "TestSender.txt"

on init
    TestSetup(0)                            { declare test variables }
    declare ui_button $Start
    declare n
end on

on ui_control($Start)

    TestStart                               { initialize test            }
    for n := 0 to 127
        Test(n)                             { show test number           }
        TestNoteOn(n, 100, 50)              { send note on signal        }
        AssertNoteOn(n)                     { the note should be played  }
        AssertVelocity(n, EqualTo, 100)     { the velocity should be 100 }
        TestNoteOff(n)                      { send note off signal       }
    end for
    TestEnd                                 { show test result           }
    
    $Start := 0
end on
------------------------------------


6. HOW TO RUN TEST SCRIPT
(1) Compile the test script by Nil's KScript Editor(http://nilsliberg.se/ksp/).
(2) Paste the compiled script to KONTAKT multi script window.
(3) Load target library and open its script editor.
(4) Paste the compiled TestReceiver script to the rightmost tab of the target library's script editor.
(5) Click start button.


7. HOW DOES IT WORK?
KSP Tester contains two script libraries TestSender and TestReceiver. TestSender 
is a KSP multi script function library. Its role is to provide testing functions 
for your test script and to run it.
TestSender executes test commands in accordance with your test script. It sends 
note messages to target library via midi and sends test messages to TestReceiver.
When test message received, TestReceiver checks status of target library and 
shows its result on the console.

              TestSender           Target Library          TestReceiver
            (run test script)
Note On/Off          --------------->
Control Change       --------------->
                                 (change status)
Test Command         ---------------------------------------->
Test Parameter       ---------------------------------------->
                                                          (check status)
                                                          (show result)


8. TEST COMMAND REFERENCE

8.1 General Commands
These are general commands defined in "TestSender.txt".

  TestInit(midi_ch)
    declaration of test variables. The argument midi_ch sets target library's channel.

  TestSetChannel(midi_ch)
    selects target library.

  TestStart
    initialization of test.

  TesEnd
    terminates test and shows result.

  Test(test_number)
    shows test number on the console. This command helps you to know test progress.

  TestNoteOn(note_no, velocity, wait)
    plays note and pauses the specified time in milliseconds.
    NOTE: TestNoteOn does not stop the note. Therefore it needs TestNoteOff.

  TestNoteOff(note_no)
    stops note.

  TestCC(byte1, byte2)
    sends midi message to target library.

  TestWait(millisecond)
    pauses the test for the specified time in milliseconds.

8.2 Assertion Commands
These are assertion commands defined in "TestSender.txt".

  AssertNoteStatus(note_no, matcher, expected_status)
    asserts note status. Status returns 1 when the note was played (0 otherwise).

  AssertNoteOn(note_no)
    same as 'AssertNoteStatus(note_no, $EqualTo, 1)'

  AssertNoteOff(note_no)
    same as 'AssertNoteStatus(note_no, $EqualTo, 0)'

  AssertGroupStatus(group_no, matcher, expected_status)
    asserts group status. Status returns 1 when the group was allowed (0 otherwise).
    If $ALL_GROUPS was specified as group_no it means at least one group was allowed
    or not.

  AssertGroupOn(group_no)
    same as 'AssertGroupStatus(group_no, $EqualTo, 1)

  AssertGroupOff(group_no)
    same as 'AssertGroupStatus(group_no, $EqualTo, 0)

  AssertGroupCounter(group_no, matcher, value)
    asserts the number of times the group was played. If $ALL_GROUPS was specified as
    group_no it means total times of all groups.

  TestGroupCounterReset
    resets counter of the number of play times.

  AssertVelocity(note_no, matcher, expected_value)
    asserts velocity value.

  AssertVelocityInRange(note_no, minvalue, maxvalue)
    asserts the velocity is between minvalue and maxvalue.

  AssertEnginePar(engine_par, group, slot, generic, matcher, value)
    asserts engine parameter value.

  AssertEngineParInRange(engine_par, group, slot, generic, minvalue, maxvalue)
    asserts the engine parameter is between minvalue and maxvalue.

  AssertPgs(index, matcher, value)
    asserts the value that sets helper function 'TestSetPgs(index, value)'.
    It allows you your GUI value test.

  AssertPgsInRange(index, minvalue, maxvalue)
    asserts the PGS value is between minvalue and maxvalue.

8.3 Matcher Constants
These are matcher constants defined in "TestCommon.txt".

  $EqualTo
  $NotEqualTo
  $LessThan
  $GreaterThan
  $LessThanOrEqualTo
  $GreaterThanOrEqualTo

8.4 Helper Function
This is a helper function defined in "TestHelper.txt".

  TestSetPgs(index, value)
    sends parameter from target library to TestReceiver via PGS.
    NOTE: Pgs key is TEST_PGS_KEY. Other key cannot be specified.


9. SYSTEM REQUIREMENT
  KONTAKT 4.0 or later
  Nil's KScript Editor (http://nilsliberg.se/ksp/)


witten by Aike
Last changed: August 9, 2011
