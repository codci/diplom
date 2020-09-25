# Cry detection test with external speakers:

## Description
Tests focus on verifying of cry detection mechanism in different snoo unit conditions and external sounds. 
??Tests using specific test setup that based on: snoo unit, sound system and PC.
More detailed information about cry setep could be founded on confluence - [sound system info]()

Sounds library - Test PC have a big cry / external sounds library, that could be used for tests. 


## Goals:
Verify cry detection mechanism with different conditions:
* internal sound (sound played from speakers that placed in snoo unit)
* external sounds (sound played from speakers around snoo unit)
* combine on internal and external sounds 


## Structure
There are three tests modules with tests for each goal: 


## Internal sound test module - [test_cry_detection.py](tests/test_cry_detection.py)
That include next tests: 

### Internal cry test (cry_test)
Test to check cry detection with different cry sounds.
During the tests the cry sounds are played from a center speaker that located on the head of snoo unit.

#### Test steps
**Precondition:** 
* Prepare audio file `cry_file` before test: copy file to temp directory and increase file length to cry detector values (30 seconds min for Baseline-Level3, 150 seconds for Level4)
* Set smconfig values for appropriated `responsiveness` value on snoo unit

**Steps:** 
1. Set `cry_lvls` and verify cry not detected in 15 seconds (without playing sound)
1. Start audio featuring if `save_audio_features` set
1. Play audio file `cry_file` and wait for cry detector event 
1. Save result of detection to dict if cry was detected or audio file finished
1. Stop playing audio file
1. Save audio features to host PC if `save_audio_features` set

Repeat steps 1-6 for each volume from `sound_volume_list`

**Post actions:**
* Delete temp audio file
* Post results of tests to cry_report.xlsx (final report)
* Finish test as successful if at least one cry was detected  

#### parameters for tests:
There are parameters that should be set for tests, information about all parameters - [README.md](./../../../../../README.md)

parameter               | description      
:---------------------- | :-----------------------------
`cry_setup`             | Name of cry setup that include Linux PC, audio system and snoo unit for tests, available setups: (cts_1, cts_2).        
`spt_device`            | SPT device name, could be used to initialize `snoo_unit` from available SPT units. All snoo dependencies will be set automatically - [SPT devices info](https://happiestbaby.atlassian.net/wiki/spaces/EN/pages/584253464/Units-under-test+connection+diagram).        
`responsiveness`        | (Not working for snoo plus units) Responsiveness level, that will set before test, by default using current snoo unit config      
`sound_volume_list`     | Volumes that will used for in cry detection tests in percents. Int value or list of volumes with `,` delimiter
`cry_lvls`              | List of snoo unit levels with `,` delimiter. By default - [baseline]  
`cry_file`              | Full path to `.wav` file with sounds or to folder with audio files. If folder path specified, then test will be run for all wav files in this directory   
`save_audio_features`   | Parameter to save audio features during cry test. Audio features will records only in cases when cry not detected    

#### Jenkins job:

* Jenkins job for run - [spe-cry_test](https://snoo-ci.happiestbaby.com/view/snoo_plus-embedded/job/snoo_plus-embedded-tests/job/snoo_plus-embedded-system_tests/view/cry_tests/job/spe-cry_test/) with next parameters:

> - `cry_test_setup`        - available setups from the list
> - `unit_serial_number`    - (Optional) in cases when we add new unit to cry setup we could set serial number of new unit
> - `cry_file`              - Path to .wav file or folder with files
> - `snoo_level`            - snoo unit levels for test from the list
> - `volume`                - volume of audio in percents from the list
> - `responsiveness`        - (Snoo one only) responsiveness level for test from the list
> - `branchname`            - branch for tests, by default develop (used for specific goals)
> - `save_audio_features`   - checkbox to save audio features during the test (if checked)

### Internal cry test for relay speaker setup (cry_test_relay)
Test to check cry detection with different cry sounds and sound direction (speaker position).
During the tests the cry sounds are played from a speakers setup that placed on the head of snoo unit with different directions.
More details about speakers setup and connection with host PC - [Relay speakers setup](https://happiestbaby.atlassian.net/wiki/spaces/EN/pages/1317274701/Test+Lab).

#### Test steps
**Precondition:** 
* Prepare audio file `cry_file` before test: copy file to temp directory and increase file length to cry detector values (30 seconds min for Baseline-Level3, 150 seconds for Level4)
* Set smconfig values for appropriated `responsiveness` value on snoo unit
* Open connection to relay board of speakers setup (`speaker_relay_ip`:`speaker_relay_port`)

**Steps:** 
1. Switch on relay board speaker to `relay_speaker`
1. Set `cry_lvls` and verify cry not detected in 15 seconds (without playing sound)
1. Start audio featuring if `save_audio_features` set
1. Play audio file `cry_file` with `volume` volume level and wait for cry detector event 
1. Save result of detection to dict if cry was detected or audio file finished
1. Stop playing audio file
1. Save audio features to host PC if `save_audio_features` set

Repeat steps 2-7 for each `volume` from `sound_volume_list`
Repeat steps 1-7 for each `relay_speaker` from `relay_speakers` list


**Post actions:**
* Delete temp audio file
* Post results of tests to cry_report.xlsx (final report)
* Finish test as successful if at least one cry was detected  

#### parameters for tests:
There are parameters that should be set for tests, information about all parameters - [README.md](./../../../../../README.md)

parameter               | description      
:---------------------- | :-----------------------------
`cry_setup`             | Name of cry setup that include Linux PC, audio system and snoo unit for tests, available setups: (cts_1, cts_2).        
`spt_device`            | SPT device name, could be used to initialize `snoo_unit` from available SPT units. All snoo dependencies will be set automatically - [SPT devices info](https://happiestbaby.atlassian.net/wiki/spaces/EN/pages/584253464/Units-under-test+connection+diagram).        
`responsiveness`        | (Not working for snoo plus units) Responsiveness level, that will set before test, by default using current snoo unit config      
`sound_volume_list`     | Volumes that will used for in cry detection tests in percents. Int value or list of volumes with `,` delimiter
`cry_lvls`              | List of snoo unit levels with `,` delimiter. By default - [baseline]  
`cry_file`              | Full path to `.wav` file with sounds or to folder with audio files. If folder path specified, then test will be run for all wav files in this directory   
`save_audio_features`   | Parameter to save audio features during cry test. Audio features will records only in cases when cry not detected    
`relay_speakers`        | Relay board speakers for test, available values: `(1, 2, 3, 4, 5, 6, 7, 8)`, by default - 1. 
`speaker_relay_ip`      | (Default config value) Ip address of relay board. Default value in config file - `embedded.cfg` 
`speaker_relay_port`    | (Default config value) Port of relay board. Default value in config file - `embedded.cfg`
`speaker_relay_type`    | (Default config value) Type of relay board (channels count), available options: `8,16`, default value in config file - `embedded.cfg`. 

#### Jenkins job:

* Jenkins job for run - [spe-internal_cry_test_relay_setup](https://snoo-ci.happiestbaby.com/view/snoo_plus-embedded/job/snoo_plus-embedded-tests/job/snoo_plus-embedded-system_tests/job/spe-internal_cry_test_relay_setup) with next parameters:

> - `cry_test_setup`        - available setups from the list
> - `cry_file`              - Path to .wav file or folder with files
> - `snoo_level`            - snoo unit levels for test from the list
> - `volume`                - volume of audio in percents from the list
> - `branchname`            - branch for tests, by default develop (used for specific goals)
> - `save_audio_features`   - checkbox to save audio features during the test (if checked)
> - `speakers`              - speakers for test by relay port from the list


## External cry test module - [test_external_sound.py](tests/test_external_sound.py)

### External sound test (external_sound_test)
Test for checking that snoo unit not detect external sounds.
During the tests sounds plays from speakers that located around snoo unit.

#### Test steps
**Precondition:** 
* Prepare audio file `external_file` before test: copy file to temp directory and increase file length to cry detector values (30 seconds min for Baseline-Level3, 150 seconds for Level4)
* Set smconfig values for appropriated `responsiveness` value on snoo unit

**Steps:** 
1. Set `cry_lvls` and verify cry not detected in 15 seconds (without playing sound)
1. Start audio featuring if `save_audio_features` set
1. Play audio file `external_file` with `volume` volume level and wait for cry detector event 
1. Save result of detection to dict if cry was detected or audio file finished
1. Stop playing audio file
1. Save audio features to host PC if `save_audio_features` set
1. Repeat steps 1-6 for each `volume` from `sound_volume_list`.
1. Repeat steps 1-7 for each `speaker` from `speakers` list


**Post actions:**
* Delete temp audio file
* Post results of tests to cry_report.xlsx (final report)
* Finish test as failed if at least one cry was detected during the test

#### parameters for tests:
There are parameters that should be set for tests, information about all parameters - [README.md](./../../../../../README.md)

parameter               | description      
:---------------------- | :-----------------------------
`cry_setup`             | Name of cry setup that include Linux PC, audio system and snoo unit for tests, available setups: (cts_1, cts_2).        
`spt_device`            | SPT device name, could be used to initialize `snoo_unit` from available SPT units. All snoo dependencies will be set automatically - [SPT devices info](https://happiestbaby.atlassian.net/wiki/spaces/EN/pages/584253464/Units-under-test+connection+diagram).        
`responsiveness`        | (Not working for snoo plus units) Responsiveness level, that will set before test, by default using current snoo unit config      
`sound_volume_list`     | Volumes that will used for in cry detection tests in percents. Int value or list of volumes with `,` delimiter
`cry_lvls`              | List of snoo unit levels with `,` delimiter. By default - [baseline]  
`external_file`         | Full path to `.wav` file with sounds or to folder with audio files. If folder path specified, then test will be run for all wav files in this directory   
`save_audio_features`   | Parameter to save audio features during cry test. Audio features will records only in cases when cry not detected    
`speakers`              | Speakers list for test, available values: `(front-left, center, front-right, side-right, rear-right, rear-left, side-left, bass)`, by default - center. 

#### Jenkins job:

* Jenkins job for run - [spe-external_sound_test](https://snoo-ci.happiestbaby.com/view/snoo_plus-embedded/job/snoo_plus-embedded-tests/job/snoo_plus-embedded-system_tests/view/cry_tests/job/spe-external_sound_test/) with next parameters:

> - `cry_test_setup`        - available setups from the list
> - `unit_serial_number`    - (Optional) in cases when we add new unit to cry setup we could set serial number of new unit
> - `external_sound_file`   - Path to .wav file or folder with files
> - `snoo_level`            - snoo unit levels for test from the list
> - `volume`                - volume of audio in percents from the list
> - `responsiveness`        - (Snoo one only) responsiveness level for test from the list
> - `speakers`              - speakers for test by speaker name from the list
> - `branchname`            - branch for tests, by default develop (used for specific goals)
> - `save_audio_features`   - checkbox to save audio features during the test (if checked)


## Extended cry test - [test_extended_cry_detection.py](tests/test_extended_cry_detection.py)

### Extended sound test (extended_cry_test)
Test for checking that snoo unit can detect cry when extended sounds are played from other sides.
During the tests cry sounds plays from center speaker and external sound played from speakers that located around snoo unit.

#### Test steps
**Precondition:** 
* Prepare audio file `external_file` before test: copy file to temp directory and increase file length to cry detector values (30 seconds min for Baseline-Level3, 150 seconds for Level4)
* Prepare audio file `cry_file` before test: copy file to temp directory and increase file length to cry detector values (30 seconds min for Baseline-Level3, 150 seconds for Level4)
* Set smconfig values for appropriated `responsiveness` value on snoo unit

**Steps:** 
1. Set `cry_lvls` and verify cry not detected in 15 seconds (without playing sound)
1. Play audio file `external_file` with `volume` volume level from external speaker - `speaker`
1. Play audio file `cry_file` with `extended_cry_volume` volume level from internal speaker
1. Wait for cry detector event 
1. Save result of detection to dict if cry was detected or audio file finished
1. Stop playing audio file
1. Repeat steps 1-6 for each `volume` from `sound_volume_list`.
1. Repeat steps 1-7 for each `speaker` from `speakers` list


**Post actions:**
* Delete temp audio files
* Post results of tests to cry_report.xlsx (final report)
* Finish test as failed if cry was not detected

#### parameters for tests:
There are parameters that should be set for tests, information about all parameters - [README.md](./../../../../../README.md)

parameter               | description      
:---------------------- | :-----------------------------
`cry_setup`             | Name of cry setup that include Linux PC, audio system and snoo unit for tests, available setups: (cts_1, cts_2).        
`spt_device`            | SPT device name, could be used to initialize `snoo_unit` from available SPT units. All snoo dependencies will be set automatically - [SPT devices info](https://happiestbaby.atlassian.net/wiki/spaces/EN/pages/584253464/Units-under-test+connection+diagram).        
`responsiveness`        | (Not working for snoo plus units) Responsiveness level, that will set before test, by default using current snoo unit config      
`sound_volume_list`     | Volumes that will used for internal speaker, value in percents. Int value
`extended_cry_volume`   | Volumes that will used for external speaker, value in percents. Int value or list of volumes with `,` delimiter
`cry_lvls`              | List of snoo unit levels with `,` delimiter. By default - [baseline]  
`external_file`         | Full path to `.wav` file with sounds or to folder with audio files - for external speakers. If folder path specified, then test will be run for all wav files in this directory   
`cry_file`              | Full path to `.wav` file with sounds or to folder with audio files - for internal speaker. If folder path specified, then test will be run for all wav files in this directory   
`speakers`              | Speakers list for test, available values: `(front-left, center, front-right, side-right, rear-right, rear-left, side-left, bass)`, by default - center. 

#### Jenkins job:

* Jenkins job for run - [spe-extended_cry_test](https://snoo-ci.happiestbaby.com/view/snoo_plus-embedded/job/snoo_plus-embedded-tests/job/snoo_plus-embedded-system_tests/view/cry_tests/job/spe-extended_cry_test) with next parameters:

> - `cry_test_setup`        - available setups from the list
> - `unit_serial_number`    - (Optional) in cases when we add new unit to cry setup we could set serial number of new unit
> - `external_file`         - Path to .wav file with external sounds or folder with files
> - `cry_file`              - Path to .wav file with cry sounds or folder with files
> - `snoo_level`            - snoo unit levels for test from the list
> - `responsiveness`        - (Snoo one only) responsiveness level for test from the list
> - `volume`                - volume of audio in percents from the list
> - `speakers`              - speakers for test by speaker name from the list
> - `branchname`            - branch for tests, by default develop (used for specific goals)


## Coverage
This test runs by request after cry detection mechanism updates or other conditions. 
There is no any specific requirements for runs.

## Results
The pytest results are demonstrative and reflect the working capacity of the test and cry_detection mechanism as a whole.

The main report file is `cry_report.xlsx` that generated after each test with detailed results of sound tests:

Content of .xlsx file split to sheets with cry detection result matrix.
Sheets are generated by responsiveness and speaker parameters.
Result matrix contains cry detection results, where: 
  * columns - speaker volume
  * rows - sound (path to audio file) + snoo level
  * values - bool value of cry detection (True / False)

Sound test results are storage on confluence - [Sound tests results](https://happiestbaby.atlassian.net/wiki/spaces/EN/pages/668271619/Sound+tests+results)
