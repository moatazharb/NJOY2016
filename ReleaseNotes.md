# Release Notes&mdash;NJOY2016
Given here are some release notes for NJOY2016. Each release is made through a formal [Pull Request](https://github.com/njoy/NJOY2016/pulls) made on GitHub. There are links in this document that point to each of those Pull Requests, where you can see in great details the changes that were made. Often the Pull Requests are made in response to an [issue](https://github.com/njoy/NJOY2016/issues). In such cases, links to those issues are also given.

## [NJOY2016.78](https://github.com/njoy/NJOY2016/pull/xxx)
This update fixes the following issues:
  - Tape numbers are now allowed to go up to 999 instead of 99 previously. This change was made to accommodate processing of the light water evaluation in ENDF/B-VIII.1 that has 94 temperatures. Test 84 was added to detect this issue in the future.
  - A few updates were made related to array sizes and array allocation.
  - Adding a message to signal malformed ENDF files when l-value lists for SLBW/MLBW and Reich-Moore have nrs=0 (no resonances given for this l value).

## [NJOY2016.77](https://github.com/njoy/NJOY2016/pull/347)
This update fixes the following issues:
  - Background cross section values for reactions other than total, elastic, fission and capture were not handled properly in the unresolved resonance region. The background for total, elastic, fission and capture is integrated into the unresolved resonance cross section values in the genunr subroutine. In the emerge subroutine, the background in the unresolved resonance region was therefore zeroed out for any resonance reaction. This has never been an issue but now we have LRF=7 evaluations that can define reactions other than total, elastic, fission and capture. Test 82 was added to detect this issue in the future.
  - Increased the size of internal arrays in VIEWR.

## [NJOY2016.76](https://github.com/njoy/NJOY2016/pull/340)
This update fixes the following issues:
  - Increase an array size to properly process Pt covariances in ENDF/B-VIII.1.

## [NJOY2016.75](https://github.com/njoy/NJOY2016/pull/335)
This update fixes the following issues:
  - Corrected a typo in the gateff subroutine in thermr (changed 1220 into 1200, see issue #76)

In addition, this update made added the following features and changes:
  - A small change to the NXS array for continuous energy and photonuclear ACE files (iopt = 1 and 5): in both cases, the isomeric state S, atom number Z and mass number A are now stored in NXS(9), NXS(10) and NXS(11) respectively.
  - A new input option for acer was introduced for setting the zaid identifier of the continuous energy and photonuclear ACE files (iopt = 1 and 5):
      - use za in the zaid regardless of the metastable state of the nuclide (default, this is how NJOY has always worked)
      - use the metastable zaid rules (MCNP 6.3 or lower) in which we use za for ground state nuclides and use za + 300 + s * 100 for metastable nuclides (Am242 and Am242m are exceptions to this rule, for these we use 95642 and 95242 respectively)

## [NJOY2016.74](https://github.com/njoy/NJOY2016/pull/327)
This update fixes the following issues:
  - Correct the Euler-Mascheroni constant (this has an influence on the Coulomb wave functions and therefore influences LRF=7 evaluations that have charged particle channels)

## [NJOY2016.73](https://github.com/njoy/NJOY2016/pull/321)
This update fixes the following issues:
  - Fix an issue in ACER for thermal scattering leading to energy values being out of order when plotting the coherent elastic scattering cross section (this issue only affects plots, the thermal scattering ACE files do not change).
  - Increased allocation of an array in LEAPR to accommodate ENDF/B-VIII.1 thermal scattering evaluations and added a check to avoid an infinite loop when using a very fine beta grid. In addition, LEAPR will now warn the user about potential excessive calculation times and print out progression in the phonon expansion sum when the phonon expansion order is large.
  - Added logic to MODER to read background R-matrix element information from LRF=7 resonance parameter data.
  - Updated RECONR to use background R-matrix element information from LRF=7 and added test 81 using ENDF/B-VIII.1 Sr88.
  - Fixing a few thing related to intel compiler warnings and errors.

## [NJOY2016.72](https://github.com/njoy/NJOY2016/pull/308)
This update fixes the following issues:
  - Fixed an issue in GROUPR related to an error coming up in production matrix calculations. Depending on when a user asks for a production matrix associated to a reaction, it is possible that the reference frame of the previous reaction is used instead (caused by erronously defining an already declared global variable as local with a "save" attribute). In some circumstances, this causes NJOY2016 to error out (with a message related to unsupported reference frames). No test results had to be updated due to this change.
  - Fixed issues in acer to properly print already existing dosimetry and photoatomic ace files when running a stand-alone acer iopt=7 job.
  - The meaning of legord and ifissp in the ERRORR input file has been repurposed when mfcov=34. The values now represent the L,L1 values of the MF34 sub-subsection to be calculated (instead of the first one). By default, the L=1,L1=1 sub-subsection will be calculated which in almost all cases will correspond to the first sub-subsection in the MF34 data (as a result, the default behaviour of NJOY2016 will not change).
  - Increased allocation of arrays to accommodate ENDF/B-VIII.1 evaluations.
  - Fixed a typo in the name for MT195 in ACER.

## [NJOY2016.71](https://github.com/njoy/NJOY2016/pull/301)
This update adds the new MF7 MT451 (thermal scattering general information) ENDF format to MODER so that this module will be able to interpret the new MF7 section. No other capability in NJOY2016 currently uses the information in this section.

This update also resolves an issue encountered when processing some JENDL5 evaluation that use LAW=7 (which ACER converts into LAW=1) due to errors in the temporary files. See issue #293 for more information.

## [NJOY2016.70](https://github.com/njoy/NJOY2016/pull/295)
This update fixes a number of minor issues:
  - Fixed an issue in HEATR when reading evaluations with large multiplicity tables in MF6.
  - Fixed an issue in HEATR when calculating the average outgoing energy from a distribution that uses multiple interpolation ranges in TAB1 records (test 79 was added to detect this issue in the future). Mainly nuclides using MF5 instead of MF6 are impacted by this change (e.g. Sn119 and Sn122 from ENDF/B-VIII.0).
  - Fixed an issue in HEATR where the photon recoil needed to be multiplied by the photon multiplicity to obtain the photon recoil per interaction.
  - Fixed a crash in THERMR when asking for S(a,b) processing (iinc=2) while no ENDF tape is given (nendf=0).
  - Multiple ERRORR calls can now be made in the same input file without crashing. This is of interest to users that wish to process MF34 and MF35 (where ERRORR needs to be called for each sub-subsection and incident energy group). The issue was related to arrays being allocated but not unallocated in the previous ERRORR run in NJOY's Sammy routines (evaluations using MF2 LRF=7 had this issue).
  - Fixed an issue in ACER where the number of photons given in the ENDF file was larger than the hardcoded limit. The new limit is now adaptive.
  - Fixed an issue in ACER where NaN values were produced in the postscript file for the checking plots.

A few compiler warnings have been resolved as well (unused variables). For source files that were corrected in this way, the remaining warnings relate to equality comparisons for real values, unused dummy arguments in subroutines and potential 0 indices into arrays (in all cases, if statements prevented this from happening).

## [NJOY2016.69](https://github.com/njoy/NJOY2016/pull/281)
This update fixes a number of minor issues:
  - PURR now writes Bondarenko data obtained from the probability tables to MF2 MT152 instead of the Bondarenko data obtained from the direct sampled cross sections (for very low dilutions, the Bondarenko data obtained using these two methods does not align, with the direct sampled data leading to extremely low P1 values). When comparing with the Bondarenko data at low dilutions obtained with UNRESR, the Bondarenko data obtained from the probability table directly seems to be the best.
  - MF6 LAW=2 represents discrete two body scattering in which only angular distribution data is given (knowing that the outgoing energy of the secondary particle can be determined through kinematics when the angle is known). When calculating heating numbers based on LAW=2, ACER assumes that the yield of the secondary particle is 1, which is correct in all cases except when MT5 is used as a lumped reaction. Heating numbers in ACER for photonuclear files using LAW=2 in an MT5 entry are now correctly multiplied by the yield. A warning message is printed out whenever this situation is detected. Test 78 was added as part of this correction.
  - Previously, ERRORR would segfault for LRF=7 resonance evaluations when MF33 was present without MF32.  A check for this situation now avoids this.
  - Fixed an issue in GROUPR when reading some of the FENDL3.2 evaluations.

A few compiler warnings have been resolved as well (unused variables). For source files that were corrected in this way, the remaining warnings relate to equality comparisons for real values, unused dummy arguments in subroutines and potential 0 indices into arrays (in all cases, if statements prevented this from happening).

## [NJOY2016.68](https://github.com/njoy/NJOY2016/pull/264)
This update fixes a number of minor issues:
  - there was an indexing error in the calculation of inelastic thermal scattering mubar in ACER for IFENG=2 ACE files. Test 74 was added to track this issue.
  - there was a division by zero that caused a segmentation fault in Debug mode, also for IFENG=2 ACE files.
  - removed an erroneously added FEND record in the PENDF file when no MF12 data gets added to the PENDF file (related to issue #250).
  - correction in ACER to fix issue #250. Previously it could happen that ACER picked the wrong temperature from a multi-temperature PENDF file when the materials in the PENDF only contained linearised MF3 data (no MF10, MF12 or MF23 data was added by RECONR).
  - fixed an indexing problem in CCCCR for delayed neutron data and added test 76.
  - there was a use of wrong memory addresses in CCCR not accounting for GROUPR's energy structure offset.

## [NJOY2016.67](https://github.com/njoy/NJOY2016/pull/243)
This update resolves a number of issues encountered when processing the newly released JENDL5 nuclear data library and TENDL-2021 library for evaluations with sub-actinide fission, and provides fixes for a few minor issues.

Due to an internal compiler error, gcc-11 will not be supported. Users should consider moving to gcc-12 or any version from gcc-7 to gcc-10.

The following minor changes and fixes are included in this version:
  - several corrections to the ERRORR source code to remove compiler warning messages (these were made while looking into issue #211 for gcc-11 compilation)
  - increased the nxcmax array size parameter in ACER to resolve issue #228
  - increased the nthr array size parameter in PURR to resolve an issue in JENDL5 Mn55 (issue #233)
  - fixed an issue for processing JENDL5 U235 in ACER (issue #237)
  - correction in HEATR to properly retrieve the fission energy release component's Q value at 0 eV (issue #209 - there currently is no evaluation impacted by this change)
  - corrected ACER following issue #188. Evaluations using LAW=7 in MF6 data that are added to the DLWH block are impacted by this change (i.e. for secondary particle types that are not the incident particle type). For ENDF/B-VIII.0, only the secondary alphas from MT16 in Be9 is impacted by the change.
  - properly set the za in the case of sub-actinide fission (MF10 MT18 data) when using MF10 automated processing in GROUPR (issue #241)
  - exclude fission from a>4 particle production matrices in GROUPR
  - when using the ign option in GROUPR for predefined group structures, some of the newer options did not return the correct structure, this has been fixed
  - an array size for reading in ENDF data was increased to accommodate a new evaluation

A large number of compiler warnings have now been resolved (unused variables, large static arrays converted into allocatable arrays, etc.). There are still a lot of warnings but their number is heavily reduced. For source files that were corrected in this way, the remaining warnings relate to equality comparisons for real values, unused dummy arguments in subroutines and potential 0 indices into arrays (in all cases, if statements prevented this from happening).

## [NJOY2016.66](https://github.com/njoy/NJOY2016/pull/226)
This constitutes a major update of NJOY to accommodate the latest IAEA photonuclear data library (which uses non-isotropic photon distributions) and the mixed mode elastic scattering ENDF format (adopted in the ENDF format in November 2020).

The following is an overview of the major changes made to NJOY for this update:
  - LAW=61 is now allowed for photonuclear ACE libraries (the secondary angular distributions are now tabulated in all cases instead of being assumed isotropic when using LAW=1 LANG=1)
  - Some photonuclear libraries use MF6/MT18 but with a neutron multiplicity equal to 1 instead of nubar. A warning is now printed when this is encountered and the multiplicity is replaced with the appropriate nubar data.
  - thermal scattering ACE files now support mixed mode elastic scattering (both coherent and incoherent elastic scattering are used), with appropriate updates to THERMR and MODER
  - The XSS array and its size is now set in the common acecm module. Writing out the ACE file and locator checking for photonuclear and thermal scattering files has been enabled as well (previously only available for incident neutron and charged particle ACE files).

In addition, the following minor issues were fixed as well:
  - fixed a typo in a reaction name printed by the ACER output (issue #195)
  - fixed a crash in ERRORR using ENDF/B-VIII.0 U235 for MF34 covariance data (issues #122 and #203)
  - increased the size of an array when processing photonuclear ACE files (issue #204)
  - extended the CLAW (iwt=9,10) weight function to cover 1e-5 -- 2e7 eV
  - fixed issues with ACELAW=33 for continuous energy neutron, charged particle and photonuclear files (issue #212)
  - fixed an issue when using the special charged particle interpolation law (INT=6) in RECONR
  - fixed an issue with MODER not being able to handle MF28 (atomic relaxation data) when converting ASCII to binary

Notes:
  - While the second minor fix no longer results in a crash of NJOY, the resulting covariance matrix is not correct since only the first subsubsection is added to the output (NJOY still calculates the matrices for each subsubsection - see issue #205)

## [NJOY2016.65](https://github.com/njoy/NJOY2016/pull/224)
This release fixes a processing issue for the d+Pt184 from TENDL-2021 in ACER. Some logic issue lead to MT11 being ignored as a deuteron producing reaction and this lead to processing problems in ACER.

This release addresses issue [\#184](https://github.com/njoy/NJOY2016/issues/184) and [\#223](https://github.com/njoy/NJOY2016/issues/223).

## [NJOY2016.64](https://github.com/njoy/NJOY2016/pull/202)
This fixes a bug in ACER when producing photonuclear ACE files using some TENDL2019 evaluations. The use of LAW=4 in MF6 of these evaluations lead to corrupted ACE files related to incorrect logic in counting secondary particle producing reactions.

This release addresses issue [\#201](https://github.com/njoy/NJOY2016/issues/201).

## [NJOY2016.63](https://github.com/njoy/NJOY2016/pull/193)
This fixes a bug in ERRORR when using the `999` option. When using this option, the input and output tapes are not closed in Fortran. This causes problems in NJOY21 as the output from ERRORR isn't completely written to disk before the next module starts. This update simply closes files `nitape` and `notape` which resolves the issue.

In addition, some logic statements in the GROUPR conver and ACER convr subroutines have been corrected to fix failures in the test suite when building in Debug mode.

## [NJOY2016.62](https://github.com/njoy/NJOY2016/pull/191)
This adds a number of changes to NJOY2016 contributed by Toshihiko Kawano, Bob McFarlane, IAEA and CIEMAT. In particular, the following changes were made:
  - fixed an issue in PURR when nunx is not set to the default value (when nunx is set to anything else but 0 or the number of energy points in the unresolved energy region, purr will now properly calculate the probability tables for the given number of tables spread out over the entire unresolved resonance region)
  - fixed an issue in RECONR where the wrong channel radius was used for the calculation of the phase shift
  - array sizes were increased
  - additional allocated arrays are now deallocated at the end of the subroutine in which they were created
  - added consistency checking for probability tables in ACER for continuous energy libraries

## [NJOY2016.61](https://github.com/njoy/NJOY2016/pull/140)
This updates the physical constants used in NJOY2016 to the CODATA2018 values recommended in the [ENDF-6 Formats Manual](https://www.nndc.bnl.gov/csewg/docs/endf-manual.pdf). The manual was updated through a format proposal at CSEWG and merged in the official repository in commit [28aca81c](https://git.nndc.bnl.gov/endf/format/endf6man/-/commit/28aca81c23d96ed783efde7f3759477972040efb).

## [NJOY2016.60](https://github.com/njoy/NJOY2016/pull/172)
When processing new incident charged particle ENDF files, NaN values were discovered in the ACE files. This appeared to be due to an array index overflow. A parameter for the size was added so that we can more easily resize the array, and an error message was added to detect similar issues in the future.

## [NJOY2016.59](https://github.com/njoy/NJOY2016/pull/170)
This release fixes a minor issue in ACER. Whenever an `ACER` check run changed the library suffix for thermal scattering libraries, it was ignored. Test 61 was added to the non regression tests.

This release addresses issue [\#163](https://github.com/njoy/NJOY2016/issues/163).

## [NJOY2016.58](https://github.com/njoy/NJOY2016/pull/169)
This release fixes a processing issue for the IRDFF-II ENDF files in GROUPR.

This release addresses issue [\#124](https://github.com/njoy/NJOY2016/issues/124).

## [NJOY2016.57](https://github.com/njoy/NJOY2016/pull/160)
This release adds a number of new predefined group structures to GROUPR and ERRORR. The code used to generate these group structures is now shared between both modules.

Please note: the meaning of ign=19 in ERRORR has changed. Previously, this meant to use a user defined structure, supplemented with the group structure read from the covariance file. This option is still available as ign=-1.

## [NJOY2016.56](https://github.com/njoy/NJOY2016/pull/147)
This relates to changes made by the consistency checks when a checker acer run is requested. NJOY only rarely modifies data. It does so for secondary particle distributions that use LAW=4 (isotropic angular distribution and continuous tabulated energy distributions) or LAW=44 (Kalbach-Mann).

When the consis routine determines the maximum outgoing energy is too high compared to epmax, it sets the last value of the outgoing energy array to this value, and all values before it to monotonically increasing values that are lower than epmax. It then adjusts the pdf values to maintain the associated cdf. This often leads to the introduction of artificial spikes in the plots produced by acer.

This behaviour has now been modified. Whenever this problem is detected, the consis routine will now reset the first energy larger to epmax to a value slightly below it and adjust the pdf value so that the cdf value for this energy point is 1.0. All other energy points after this energy point along with their associated data is set to zero. The locators in the xss array are left unchanged.

Due to this new behaviour, and because the routine that writes the xss array does not check locators, the change routine was modified as well to verify consistency of the locators with the actual position in the xss array. Whenever the locator is inconsistent, it will either just advance to the locator (thus taking into account the gaps created by the consis routine) or issue an error when the locator points to a position lower than the current one.

The change routine was also simplified to improve its readability by adding subroutines to write a list of integers, a list of real numbers, a single integer and a single real number.

A large number of compiler warnings were also fixed in this pull request (mostly unused or uninitialised variables).

Test case 55 was added to properly test this for a LAW=44.

This release addresses issue [\#130](https://github.com/njoy/NJOY2016/issues/130).

## [NJOY2016.55](https://github.com/njoy/NJOY2016/pull/146)
For incident alpha particles, acer assumes that when MT22 is present, the MF6 entry also contains the outgoing alphas for this reaction. When an evaluation is incomplete because this outgoing particle is missing from the MF6 section, this is not detected and thus results in a corrupted ace file. This can generally happen for any incident particle type. This release detects the absence of the outgoing particle and terminates NJOY gracefully.

This release addresses issue [\#143](https://github.com/njoy/NJOY2016/issues/143).

## [NJOY2016.54](https://github.com/njoy/NJOY2016/pull/145)
A division by zero can occur in HEATR when the atomic mass of the outgoing particles in MF6 subsections in zero. As it is impossible to recover from this (i.e. we cannot guess the atomic weight the evaluator wanted to use), this release allows NJOY to exit gracefully and report the MT number and secondary particle that caused the issue.

This pull request addresses issue [\#144](https://github.com/njoy/NJOY2016/issues/144).

## [NJOY2016.53](https://github.com/njoy/NJOY2016/pull/141)
In an initial proposal for NJOY2016.51, the number of za values to be read (the nza variable) was explicitly requested in the input. This was removed in favor of reading 16 values by default and then determining how many were given. This proves problematic for NJOY21. As a result, specifying nza is now mandatory again.

## [NJOY2016.52](https://github.com/njoy/NJOY2016/pull/139)
An error was corrected in the calculation of the Coulomb elastic scattering cross section for incident charged particles. The issue was detected when producing an ACE file for alpha on alpha with ACER but the error also existed in GROUPR. All instances have been corrected. Tests were added for 5 cases to detect this issue in the future.

This pull request addresses issue [\#138](https://github.com/njoy/NJOY2016/issues/138).

## [NJOY2016.51](https://github.com/njoy/NJOY2016/pull/127)
Changes were made to ACER to increase the number of ZA values that go into the ACE file for a thermal scattering file from 3 to 16 values. This was an issue when one tried to specify the ZA values for a thermal scattering file such as ZrH or SiO2 (the additional ZA values were added by hand as required).

This pull request addresses issue [\#126](https://github.com/njoy/NJOY2016/issues/126).

## [NJOY2016.50](https://github.com/njoy/NJOY2016/pull/136)
Changes were made to the ACER routines for photoatomic data to fix an infinite loop when using ENDF/B-VIII.0 data and to fix the scratch array size to accommodate the current size of the ENDF data. An additional test was added to detect this problem in the future.

This pull request addresses issue [\#91](https://github.com/njoy/NJOY2016/issues/91) and  [\#135](https://github.com/njoy/NJOY2016/issues/135).

## [NJOY2016.49](https://github.com/njoy/NJOY2016/pull/119)
GROUPR was modified to produce MF6 MT18 data for each sigma0 value. A test was added to verify that this does not impact ERRORR processing. Other tests were modified to also provide the GROUPR GENDF file in ASCII for testing this new feature.

This pull request addresses issue [\#118](https://github.com/njoy/NJOY2016/issues/118)

## [NJOY2016.48](https://github.com/njoy/NJOY2016/pull/117)
The changes are as follows:

- a message is issued when the background plus the potential scattering, the interference and sigma0 value are negative
- a message is issued when the beta value is within the -1 to 0 range (which causes a sqrt to return NaN)

Questions:

- the first message could be slightly changed so that it is issued when the background plus the potential scattering and the interference are negative, regardless of the value of sigma0. In my opinion, the fact that this value is negative before adding sigma0 is an indication that the evaluation is no good whatever happens.
- the second message could be transformed into an error and that would stop the processing dead in its tracks (which degrades the behaviour from before these changes - it was silently ignored). In my opinion, the results will be inherently wrong, which would merit the use of error instead of mess.

This Pull Request addresses issue [\#116 (GROUPR has problems due to NaNs coming from UNRESR)](https://github.com/njoy/NJOY2016/issues/116). Look there for more information.

## [NJOY2016.47](https://github.com/njoy/NJOY2016/pull/115)
Additional fixes to the physical constants: electron and helion mass. See issue [\#106](https://github.com/njoy/NJOY2016/issues/106).

No impact on test results (no changes, these constants do not seem to be used in any of the tests we currently run).

## [NJOY2016.46](https://github.com/njoy/NJOY2016/pull/109)
This is a companion PR to [njoy/NJOY2016-manual#23](https://github.com/njoy/NJOY2016-manual/pull/23), updating the comment block in leapr.f90.

## [NJOY2016.45](https://github.com/njoy/NJOY2016/pull/113)
This should resolve issue [\#105 (A Simple Bug in NJOY2016 and NJOY2012)](https://github.com/njoy/NJOY2016/issues/105).

## [NJOY2016.44](https://github.com/njoy/NJOY2016/pull/107)
This fixes issue [\#106 (Typo in hbar)](https://github.com/njoy/NJOY2016/issues/106)
