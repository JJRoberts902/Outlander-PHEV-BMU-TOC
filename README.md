# Mitsubishi Outlander PHEV BMU Definitions

## Definition purpose reference

This guide explains every top-level definition in the current 2020 Mitsubishi Outlander BMU XML for the M32R processor family. It follows the category overview and table-by-table layout of the SST definition guide, with a focus on what each BMU item does and how it relates to battery management.

**Coverage:** 230 top-level entries, including calibration maps, scalar controls, diagnostic data, identification records and communication configuration. Child axis definitions are described with their parent entries rather than counted as additional maps.

This inventory covers the current XML; it does not claim that every tunable item in the firmware has been identified. Some entries have verified functional roles but unresolved physical subsystem or fault identities. Those limits are stated below.

## How to read this guide

Map inputs follow the axis names in the XML. Scalar entries have no external axis. Record and channel indices identify stored lists rather than physical lookup quantities. Native counts remain counts where the scheduler period or physical conversion has not been established.

Identification records and communication configuration are included because they are present in the XML, although they serve a different purpose from battery calibration maps. State-latch letters and channel groups describe decoded firmware logic; they do not establish factory fault names.

Address-bearing diagnostic labels are replaced here with descriptive names or lettered reference labels. Lettered defaults and delay factors follow their order in the XML category. Duplicate pack-current condition names are distinguished as first and second entries. These documentation labels do not rename the XML entries.

## Capacity and SOC for replacement battery packs

| Definition group | Purpose when reviewing a higher-capacity pack |
|---|---|
| Nominal capacity and diagnostic capacity default | Defines the nominal Ah reference used by the capacity path. Capacity and SOC percent represent different quantities. |
| Diagnostic capacity validity bounds and fallback | Determines which diagnostic capacity values are accepted and which replacement value is used when they are rejected. Review together so a larger valid capacity is not rejected by the existing range. |
| Capacity sample bounds and qualification thresholds | Governs acceptance and qualification of capacity inputs in separate paths. These require review alongside the nominal reference. |
| Voltage/C-rate SOC map and SOC tracking gain | Describes the voltage/load-to-SOC relationship and tracking correction. A larger pack requires consideration of its actual cell behavior and current normalization; scaling SOC percentages by the Ah increase is not an equivalent calibration. |
| Stored voltage-derived SOC bounds, selection ceiling and exclusion bands | Controls validity and selection between stored and interpolated SOC. These are estimator controls, separate from cell-voltage protection boundaries. |
| Capacity-loss, retention and throughput maps | Describes modeled aging and retained capacity. Changing nominal capacity alone does not establish that the existing aging model matches different cells. |
| Current sensing, resistance and charge/discharge capability | Connects measured current and modeled battery behavior to available capability. Larger Ah capacity alone does not establish a higher permitted current or power. |
| Series cell count and configuration selector | Describes electrical topology. A pack with more Ah but the same series arrangement does not require a different series cell count solely because its capacity increased. |

The current XML retains an unresolved byte-order discrepancy in the shared Ah capacity scaling. Displayed Ah values must be confirmed before using the capacity entries. This README documents their intended roles and does not modify that scaling. A nominal-capacity edit also does not, by itself, establish a procedure for updating retained learned battery state.

## Category overview

| Category | Entries | Purpose |
|---|---:|---|
| BMU Information | 2 | Controller identification and language reference. |
| Firmware identification | 4 | Firmware/build identification records. |
| CAN-IDS | 7 | CAN slot identifiers and payload lengths. |
| State of Charge | 1 | Cell-voltage and C-rate SOC lookup. |
| Voltage Limits | 3 | Voltage references and temperature-dependent parameters. |
| Battery Current | 2 | High-range and low-range current sensor calibration. |
| Thermal Controls | 2 | Temperature-dependent correction factors. |
| Battery Controls | 3 | Temperature, current and voltage-dependent control lookups. |
| Calibration maps | 13 | Output schedules, controller gains and condition qualification. |
| SOC and capacity estimation | 5 | SOC tracking, modeled aging, resistance and throughput exposure. |
| Battery configuration | 1 | Configuration-indexed series cell count. |
| Current sensing | 2 | Current sensor transfer functions. |
| Analog signal conversion | 4 | ADC and local temperature conversion. |
| Charge and discharge capability | 3 | Temperature/SOC-dependent charge and discharge capability. |
| Diagnostic defaults | 25 | Fallback data and diagnostic snapshot selection. |
| Diagnostic monitors | 19 | Fault qualification, self-checks, delays and service settings. |
| Diagnostic identity - read reference | 11 | Identity values returned through diagnostics. |
| Diagnostic trouble-code encoding | 1 | Encoded trouble-code identifiers. |
| CAN1 configuration | 7 | Mailbox modes, identifiers and payload lengths. |
| CAN0 descriptors | 10 | Frame descriptors, completion status and receive routing. |
| CAN0 hardware configuration | 8 | Initial CAN configuration, bit-rate prescaler and acceptance masks. |
| CAN0 scheduler | 4 | Periodic transmit counters and descriptor order. |
| Serial diagnostic protocol | 2 | Response headers and minimum delays. |
| Module and channel routing | 4 | Module-to-channel frame and data routing. |
| Module polling | 3 | Polling mailbox selection and counters. |
| Capacity configuration | 8 | Nominal capacity, acceptable capacity ranges and fallback. |
| Power and current limit configuration | 9 | Voltage-margin candidates, clamps, filtering and source selection. |
| Cell configuration | 1 | Series-cell configuration selection. |
| Cell voltage monitor thresholds | 9 | Voltage status and qualification boundaries. |
| Cell temperature monitor thresholds | 7 | Temperature status, qualification, hysteresis and override. |
| Pack current monitor thresholds | 3 | Pack-current condition and monitor boundaries. |
| SOC diagnostic configuration | 3 | Diagnostic SOC input validity and fallback. |
| State qualification counts | 15 | Persistence thresholds for decoded state latches. |
| State qualification controls | 4 | Set/reset update gates for decoded state latches. |
| Channel status diagnostics | 16 | Grouped status and mismatch qualification. |
| SOC validity and selection | 9 | Stored voltage-derived SOC validation and estimator selection. |

## Complete definition-by-definition purpose reference

The reference numbers below identify README rows only. Every top-level XML entry appears once, including separate entries that describe related functions.

### BMU Information

Controller identification and language reference.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 1 | BMU ID | 1D | Single value / record | Identifies the BMU firmware through its stored identification text; useful for matching a definition to the intended controller. |
| 2 | BMU_Language_Type | 2D | X (16 points) | Identifies the firmware language/type text; a reference item rather than a battery-behavior adjustment. |

### Firmware identification

Firmware/build identification records.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 3 | Firmware identification record | 2D | Byte (32 points) | Provides the firmware identification record used to distinguish software versions. |
| 4 | Language record | 2D | Byte (16 points) | Provides the language record associated with the firmware identification data. |
| 5 | Build tag | 2D | Byte (8 points) | Provides the firmware build tag for software identification. |
| 6 | Build version | 2D | Byte (8 points) | Provides the firmware build version for software identification. |

### CAN-IDS

CAN slot identifiers and payload lengths.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 7 | Standard ID low byte | 2D | CAN slot (20 points) | Defines the low byte of each slot's standard CAN identifier. |
| 8 | Standard ID High / Flags | 2D | CAN slot (20 points) | Defines the high identifier bits and associated flags for each CAN slot. |
| 9 | Starting Standard ID Base Call | 2D | CAN slot (20 points) | Provides the starting standard-identifier base used by the configured CAN setup. |
| 10 | EID0 by slot | 2D | CAN slot (20 points) | Defines the first extended-identifier component for each CAN slot. |
| 11 | EID1 by slot | 2D | CAN slot (20 points) | Defines the second extended-identifier component for each CAN slot. |
| 12 | EID2 by slot | 2D | CAN slot (20 points) | Defines the third extended-identifier component for each CAN slot. |
| 13 | DLC by slot | 2D | CAN slot (20 points) | Defines the CAN payload length for each slot. |

### State of Charge

Cell-voltage and C-rate SOC lookup.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 14 | BMU State of Charge from Cell Voltage and C Rate | 3D | Battery current C-rate (9 points); Cell voltage (65 points) | Estimates SOC from cell voltage and battery current expressed as C-rate. This surface describes the voltage-to-SOC relationship under the indexed load conditions. |

### Voltage Limits

Voltage references and temperature-dependent parameters.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 15 | BMU Cell Voltage Reference vs Temperature and C Rate | 3D | Battery current C-rate (9 points); Battery temperature (56 points) | Supplies a cell-voltage reference indexed by battery temperature and C-rate for the voltage-dependent control path. |
| 21 | BMU Temperature Gated Cell Voltage Parameter #1 | 2D | Battery temperature (18 points) | Supplies the first temperature-dependent cell-voltage parameter used by a gated control path; it is not identified here as a universal voltage cutoff. |
| 22 | BMU Temperature Gated Cell Voltage Parameter #2 | 2D | Battery temperature (18 points) | Supplies the second temperature-dependent cell-voltage parameter used by a gated control path. |

### Battery Current

High-range and low-range current sensor calibration.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 16 | BMU High Range Battery Current Sensor Linearisation | 2D | Current sensor voltage (7 points) | Converts the high-range current-sensor input into the calibrated current representation. |
| 17 | BMU Low Range Battery Current Sensor Linearisation | 2D | Current sensor voltage (7 points) | Converts the low-range current-sensor input into the calibrated current representation, particularly relevant to smaller currents. |

### Thermal Controls

Temperature-dependent correction factors.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 18 | BMU Temperature Correction Factor #1 | 2D | Battery temperature (implicit -30 to 5 degC) (13 points) | Supplies the first temperature-dependent correction factor. The exact downstream physical role is not established by the label alone. |
| 19 | BMU Temperature Correction Factor #2 | 2D | Battery temperature (implicit -30 to 5 degC) (13 points) | Supplies the second temperature-dependent correction factor. Treat it separately from the first correction table. |

### Battery Controls

Temperature, current and voltage-dependent control lookups.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 20 | BMU Temperature Dependent Control Lookup | 2D | Battery temperature (4 points) | Supplies a temperature-indexed control value. The final controlled physical quantity remains unresolved. |
| 23 | BMU Current Dependent Correction Factor | 2D | Battery current (14 points) | Supplies a correction factor indexed by battery current; its precise downstream physical role remains unresolved. |
| 24 | BMU Cell Voltage Dependent Operating Point Parameter | 2D | Cell voltage (stored mV) (10 points) | Supplies a cell-voltage-indexed operating-point parameter; its precise downstream physical role remains unresolved. |

### Calibration maps

Output schedules, controller gains and condition qualification.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 25 | Minimum-temperature output schedule | 2D | Selected minimum battery temperature (19 points) | Schedules an output from the selected minimum battery temperature. The output's physical actuator identity remains unresolved. |
| 26 | External-controller accumulated duty-correction increment | 2D | External controller error (15 points) | Sets the increment applied to the external-controller accumulated duty correction, shaping the accumulated correction term. |
| 27 | External-controller proportional duty-correction gain | 2D | Filtered external controller error (15 points) | Sets the proportional duty-correction gain for the external-controller error path. |
| 28 | Timer-output duty feedforward vs external setpoint | 2D | External controller setpoint (11 points) | Provides the baseline timer-output duty corresponding to an external setpoint, before feedback corrections. |
| 30 | Current-band qualification duration vs temperature | 2D | Minimum cell temperature (5 points) | Sets a temperature-dependent qualification threshold for a current-band condition. A conversion to elapsed seconds has not been established. |
| 36 | Low-current qualification duration vs temperature | 2D | Minimum cell temperature (13 points) | Sets the qualification requirement for a low-current condition as battery temperature changes. |
| 45 | Operating-state qualification duration vs temperature | 2D | Minimum cell temperature (13 points) | Sets the temperature-dependent qualification requirement for an operating-state condition. |
| 46 | SOC-state qualification duration vs temperature | 2D | Minimum cell temperature (13 points) | Sets the temperature-dependent qualification requirement for an SOC-state condition. |
| 47 | Stability qualification delay vs temperature | 2D | Battery temperature (5 points) | Sets the temperature-dependent delay threshold used to qualify a stable condition. |
| 48 | Low-current qualification duration 1 vs temperature | 2D | Minimum cell temperature (13 points) | Sets the first temperature-dependent low-current qualification requirement. |
| 49 | Low-current qualification duration 2 vs temperature | 2D | Minimum cell temperature (13 points) | Sets the second temperature-dependent low-current qualification requirement; this is a separate qualification path. |
| 50 | Low-current qualification threshold 1 vs temperature | 2D | Battery temperature (13 points) | Sets the first temperature-dependent current boundary used by low-current qualification. |
| 51 | Low-current qualification threshold 2 vs temperature | 2D | Battery temperature (13 points) | Sets the second temperature-dependent current boundary used by low-current qualification. |

### SOC and capacity estimation

SOC tracking, modeled aging, resistance and throughput exposure.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 29 | SOC tracking integration gain adjustment | 3D | SOC (11 points); SOC difference (15 points) | Adjusts the SOC tracking integration gain according to SOC and SOC difference, influencing how the tracking correction evolves. |
| 31 | Time-dependent capacity-loss coefficient vs SOC and temperature | 3D | Selected maximum battery temperature (4 points); SOC (11 points) | Supplies the time-dependent capacity-loss coefficient according to SOC and maximum battery temperature, contributing to the modeled aging estimate. |
| 32 | Capacity-retention factor vs aggregate exposure | 2D | Aggregate exposure index (112 points) | Converts accumulated exposure into a capacity-retention factor used by the capacity model. |
| 33 | Minimum cell-resistance floor vs temperature | 2D | Minimum cell temperature (10 points) | Sets a temperature-dependent minimum resistance value used by the cell-resistance model. |
| 43 | Current-throughput exposure contribution 2 | 2D | Firmware-accumulated current throughput 2 (56 points) | Supplies the second current-throughput exposure contribution used by the capacity/aging model. |

### Battery configuration

Configuration-indexed series cell count.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 34 | Series cell count by battery configuration | 2D | Battery configuration index (8 points) | Provides the series cell count for the selected battery configuration; this describes pack topology rather than Ah capacity. |

### Current sensing

Current sensor transfer functions.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 35 | High-range current sensor transfer | 2D | Current sensor voltage (7 points) | Defines the high-range current sensor's calibrated input-to-current transfer relationship. |
| 39 | Low-range current sensor transfer | 2D | Current sensor voltage (7 points) | Defines the low-range current sensor's calibrated input-to-current transfer relationship. |

### Analog signal conversion

ADC and local temperature conversion.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 37 | ADC channel 11 monitor conversion | 2D | Nominal ADC input voltage (2 points) | Converts ADC channel 11's input into its monitor representation. The specific physical sensor assignment remains unresolved. |
| 38 | ADC channels 0 and 4 supply-monitor conversion | 2D | Nominal ADC input voltage (8 points) | Converts the supply-monitor inputs associated with ADC channels 0 and 4 into the monitored representation. |
| 44 | ADC channel 1 monitor conversion | 2D | Nominal ADC input voltage (2 points) | Converts ADC channel 1's input into its monitor representation. The specific physical sensor assignment remains unresolved. |
| 185 | Local analog temperature conversion | 2D | Ratiometric nominal ADC input voltage (12 points) | Converts the ratiometric nominal ADC input voltage into a local analog temperature representation. The exact sensor identity and this inherited table's scaling still require confirmation. |

### Charge and discharge capability

Temperature/SOC-dependent charge and discharge capability.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 40 | Cell charge current limit vs temperature | 2D | Battery temperature (54 points) | Sets the temperature-dependent cell charge-current capability used by the charge-limit calculation. |
| 41 | Cell charge power limit vs SOC and temperature | 3D | Cell temperature (5 points); SOC (11 points) | Sets cell charge-power capability according to SOC and temperature, shaping the power permitted by the modeled battery condition. |
| 42 | Cell discharge power limit vs SOC and temperature | 3D | Cell temperature (5 points); SOC (11 points) | Sets cell discharge-power capability according to SOC and temperature, shaping the power permitted by the modeled battery condition. |

### Diagnostic defaults

Fallback data and diagnostic snapshot selection.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 52 | Paired voltage-derived SOC default factor | 1D | Single value / record | Provides the fallback factor for the paired stored voltage-derived SOC inputs. Firmware converts the byte factor into SOC percent; this is separate from nominal Ah capacity. |
| 53 | Diagnostic word default factor A | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 54 | Paired diagnostic word default A | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 55 | Diagnostic word default B | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 56 | Diagnostic byte default A | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 57 | Diagnostic word default C | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 58 | Ten-channel diagnostic byte defaults | 1D | Single value / record | Provides a bank of ten default byte values for the corresponding channel diagnostic outputs. |
| 59 | Diagnostic byte default B | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 60 | Diagnostic word default D | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 61 | Diagnostic word default E | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 62 | Diagnostic byte default C | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 63 | Diagnostic byte default D | 1D | Single value / record | Provides a default diagnostic byte used by an override path shared with the diagnostic snapshot selector; its physical signal meaning remains unresolved. |
| 64 | Diagnostic snapshot word default | 1D | Single value / record | Provides the diagnostic snapshot word selected by the default override selector instead of the word assembled from runtime status flags. |
| 65 | Diagnostic word default F | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 66 | Diagnostic 32-bit default A | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 67 | Diagnostic 32-bit default B | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 68 | Diagnostic 32-bit default C | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 69 | Diagnostic 32-bit default D | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 70 | Diagnostic 32-bit default E | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 71 | Diagnostic 32-bit default F | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 72 | Diagnostic 32-bit default G | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 73 | Diagnostic byte default E | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 74 | Diagnostic word default G | 1D | Single value / record | Provides the default value for the corresponding diagnostic item when the firmware selects its default path. The physical meaning of this item remains unresolved. |
| 75 | Ten-channel diagnostic word defaults | 1D | Single value / record | Provides a bank of ten default word values for the corresponding channel diagnostic outputs. |
| 221 | Diagnostic snapshot default override selector | 1D | Single value / record | Selects the diagnostic snapshot's default word when nonzero; zero assembles the word from twelve runtime flags. Also affects a separate diagnostic default-override path. |

### Diagnostic monitors

Fault qualification, self-checks, delays and service settings.

Some inherited XML labels describe zero as disabled. A zero count or factor does not establish the same behavior for every monitor; the relevant consumer logic determines its effect.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 76 | Fault monitor A initial cycles | 1D | Single value / record | Sets the initial cycle-count qualification for fault monitor A. Its exact physical fault assignment remains unresolved. |
| 77 | Fault monitor A holdoff cycles | 1D | Single value / record | Sets the holdoff cycle count for fault monitor A, affecting its monitoring sequence. |
| 78 | Fault monitor A follow-up cycles | 1D | Single value / record | Sets the follow-up cycle count for fault monitor A after the earlier monitor stages. |
| 79 | Fault monitor B initial cycles | 1D | Single value / record | Sets the initial cycle-count qualification for fault monitor B. Its exact physical fault assignment remains unresolved. |
| 80 | Fault monitor B holdoff cycles | 1D | Single value / record | Sets the holdoff cycle count for fault monitor B, affecting its monitoring sequence. |
| 81 | Fault monitor B follow-up cycles | 1D | Single value / record | Sets the follow-up cycle count for fault monitor B after the earlier monitor stages. |
| 82 | ROM self-check failure boot counter threshold | 1D | Single value / record | Sets the boot-count threshold associated with ROM self-check failures. |
| 83 | RAM self-check failure boot counter threshold | 1D | Single value / record | Sets the boot-count threshold associated with RAM self-check failures. |
| 84 | ROM checksum monitor fault count threshold | 1D | Single value / record | Sets the fault-count threshold used by the ROM checksum monitor. |
| 85 | Diagnostic state A timeout factor | 1D | Single value / record | Scales the timeout used by diagnostic state A; the state's exact physical role remains unresolved. |
| 86 | Diagnostic state B timeout factor | 1D | Single value / record | Scales the timeout used by diagnostic state B; the state's exact physical role remains unresolved. |
| 87 | ROM self-check diagnostic enable | 1D | Single value / record | Controls the diagnostic enable setting associated with the ROM self-check. |
| 88 | RAM self-check diagnostic enable | 1D | Single value / record | Controls the diagnostic enable setting associated with the RAM self-check. |
| 89 | Additional monitor enable | 1D | Single value / record | Controls an additional monitor's enable setting; the physical monitor assignment remains unresolved. |
| 90 | Service setting boolean | 1D | Single value / record | Provides a boolean service setting used by the firmware; the exact service function remains unresolved. |
| 91 | Generated delay factor 1 | 1D | Single value / record | Supplies the first generated delay factor. Its physical subsystem and elapsed-time conversion remain unresolved. |
| 92 | Generated delay factor 2 | 1D | Single value / record | Supplies the second generated delay factor. Its physical subsystem and elapsed-time conversion remain unresolved. |
| 93 | Generated delay factor 3 | 1D | Single value / record | Supplies the third generated delay factor. Its physical subsystem and elapsed-time conversion remain unresolved. |
| 94 | Generated delay factor 4 | 1D | Single value / record | Supplies the fourth generated delay factor. Its physical subsystem and elapsed-time conversion remain unresolved. |

### Diagnostic identity - read reference

Identity values returned through diagnostics.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 95 | Diagnostic response hardware value | 1D | Single value / record | Provides the hardware-identification value returned by a diagnostic response; useful as a read reference. |
| 96 | Diagnostic identity response word | 1D | Single value / record | Provides the word component of a firmware-defined diagnostic identity response. |
| 97 | Diagnostic identity response byte 2 | 1D | Single value / record | Provides byte component 2 of the same diagnostic identity response. |
| 98 | Diagnostic identity response byte 1 | 1D | Single value / record | Provides byte component 1 of the same diagnostic identity response. |
| 99 | Diagnostic identity word | 1D | Single value / record | Provides a diagnostic identity word used to identify the controller/software. |
| 100 | Diagnostic identity A prefix | 1D | Single value / record | Provides the prefix of diagnostic identity record A. |
| 101 | Diagnostic identity A middle | 1D | Single value / record | Provides the middle portion of diagnostic identity record A. |
| 102 | Diagnostic identity A suffix | 1D | Single value / record | Provides the suffix of diagnostic identity record A. |
| 103 | Diagnostic identity B prefix | 1D | Single value / record | Provides the prefix of diagnostic identity record B. |
| 104 | Diagnostic identity B middle | 1D | Single value / record | Provides the middle portion of diagnostic identity record B. |
| 105 | Diagnostic identity B suffix | 1D | Single value / record | Provides the suffix of diagnostic identity record B. |

### Diagnostic trouble-code encoding

Encoded trouble-code identifiers.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 106 | Diagnostic trouble-code identifier table | 2D | Index (874 points) | Maps the firmware's trouble-code records to their encoded diagnostic identifiers. It is an encoding reference, not a fault-disable table. |

### CAN1 configuration

Mailbox modes, identifiers and payload lengths.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 107 | CAN1 Mailbox mode/control bytes | 2D | Index (32 points) | Defines the mailbox mode/control bytes loaded into CAN1's mailbox configuration. |
| 108 | CAN1 Standard identifier register SID0 | 2D | Index (32 points) | Defines the first standard-identifier register component for CAN1 mailboxes. |
| 109 | CAN1 Standard identifier register SID1 | 2D | Index (32 points) | Defines the second standard-identifier register component for CAN1 mailboxes. |
| 110 | CAN1 Extended identifier register EID0 | 2D | Index (32 points) | Defines the first extended-identifier register component for CAN1 mailboxes. |
| 111 | CAN1 Extended identifier register EID1 | 2D | Index (32 points) | Defines the second extended-identifier register component for CAN1 mailboxes. |
| 112 | CAN1 Extended identifier register EID2 | 2D | Index (32 points) | Defines the third extended-identifier register component for CAN1 mailboxes. |
| 113 | CAN1 Data length code | 2D | Index (32 points) | Defines the payload length configured for CAN1 mailboxes. |

### CAN0 descriptors

Frame descriptors, completion status and receive routing.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 114 | CAN0 TX completion status byte index | 2D | Index (46 points) | Selects the status-byte index used to track completion of each CAN0 transmission. |
| 115 | CAN0 TX completion status bit mask | 2D | Index (46 points) | Defines the bit mask used to test or update each CAN0 transmit-completion status. |
| 116 | CAN0 RX completion status byte index | 2D | Index (25 points) | Selects the status-byte index used to track completion of each CAN0 reception. |
| 117 | CAN0 RX completion status bit mask | 2D | Index (25 points) | Defines the bit mask used to test or update each CAN0 receive-completion status. |
| 118 | CAN0 TX encoded standard identifier words | 2D | Index (46 points) | Defines the encoded standard identifiers associated with CAN0 transmit descriptors. |
| 119 | CAN0 TX DLC bytes | 2D | Index (46 points) | Defines the payload length for each CAN0 transmit descriptor. |
| 120 | CAN0 TX inhibit masks | 2D | Index (46 points) | Defines the inhibit masks used to gate CAN0 transmissions under the firmware's runtime conditions. |
| 121 | CAN0 mailbox to RX descriptor routing | 2D | Index (32 points) | Routes each CAN0 mailbox to its receive descriptor so received frames reach the intended handling path. |
| 122 | CAN0 RX encoded standard identifier words | 2D | Index (25 points) | Defines the encoded standard identifiers associated with CAN0 receive descriptors. |
| 123 | CAN0 RX minimum DLC and copy lengths | 2D | Index (25 points) | Defines the minimum accepted payload lengths and the data-copy lengths used by CAN0 receive handling. |

### CAN0 hardware configuration

Initial CAN configuration, bit-rate prescaler and acceptance masks.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 124 | CAN0 configuration register initial word | 1D | Single value / record | Provides the initial word loaded into CAN0's configuration register. |
| 125 | CAN0 bit rate prescaler initial byte | 1D | Single value / record | Provides the initial CAN0 bit-rate prescaler value used with the controller's timing configuration. |
| 126 | CAN0 global standard mask SID0 | 1D | Single value / record | Defines the first global standard-identifier acceptance-mask component for CAN0. |
| 127 | CAN0 global standard mask SID1 | 1D | Single value / record | Defines the second global standard-identifier acceptance-mask component for CAN0. |
| 128 | CAN0 local standard mask SID0 | 1D | Single value / record | Defines the first local standard-identifier acceptance-mask component for CAN0. |
| 129 | CAN0 local standard mask SID1 | 1D | Single value / record | Defines the second local standard-identifier acceptance-mask component for CAN0. |
| 130 | CAN0 special receive SID0 | 1D | Single value / record | Defines the first standard-identifier component used by CAN0's special receive configuration. |
| 131 | CAN0 special receive SID1 | 1D | Single value / record | Defines the second standard-identifier component used by CAN0's special receive configuration. |

### CAN0 scheduler

Periodic transmit counters and descriptor order.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 132 | CAN0 periodic transmit descriptor order | 2D | Index (31 points) | Defines the order in which periodic CAN0 transmit descriptors are serviced. |
| 133 | CAN0 periodic transmit initial counters | 2D | Index (31 points) | Provides the initial counters used by the CAN0 periodic transmit scheduler. |
| 134 | CAN0 periodic transmit reload counters | 2D | Index (31 points) | Provides the reload counters used to repeat scheduled CAN0 transmissions; elapsed intervals depend on the scheduler period. |
| 135 | CAN0 receive status reset descriptor order | 2D | Index (9 points) | Defines the descriptor order used when resetting CAN0 receive status. |

### Serial diagnostic protocol

Response headers and minimum delays.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 136 | Serial diagnostic response minimum delays | 2D | Index (4 points) | Sets minimum response delays for the serial diagnostic protocol. |
| 137 | Serial diagnostic response header bytes | 2D | Index (4 points) | Provides the header bytes used to construct serial diagnostic responses. |

### Module and channel routing

Module-to-channel frame and data routing.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 138 | Module frame output routing | 3D | Frame group (0..4) (5 points); Module index (0..11) (12 points) | Routes module frames to their configured output destinations. |
| 139 | Channel frame group selectors | 2D | Index (12 points) | Selects the frame group associated with each channel. |
| 140 | Module word channel destination routing | 3D | Word channel (0..11) (12 points); Module index (0..11) (12 points) | Routes word-sized module data to the corresponding channel destinations. |
| 141 | Module byte channel destination routing | 3D | Byte channel (0..5) (12 points); Module index (0..11) (6 points) | Routes byte-sized module data to the corresponding channel destinations. |

### Module polling

Polling mailbox selection and counters.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 142 | Module poll mailbox selectors | 2D | Index (2 points) | Selects the mailboxes used by the module polling routine. |
| 143 | Module poll initial counters | 2D | Index (2 points) | Provides initial counters used when module polling starts. |
| 144 | Module poll retry reload counters | 2D | Index (2 points) | Provides the counter reload values used for module polling retries. |

### Capacity configuration

Nominal capacity, acceptable capacity ranges and fallback.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 145 | Nominal capacity and diagnostic capacity default | 1D | Single value / record | Sets the nominal Ah capacity reference and the associated default diagnostic capacity. This is a key capacity-model input for a replacement pack. |
| 146 | Diagnostic capacity upper validity bound | 1D | Single value / record | Sets the largest diagnostic capacity value accepted by this validation path before fallback is used. |
| 147 | Diagnostic capacity lower validity bound | 1D | Single value / record | Sets the smallest diagnostic capacity value accepted by this validation path before fallback is used. |
| 148 | Diagnostic capacity fallback | 1D | Single value / record | Provides the capacity used when the diagnostic capacity fails its validity checks. |
| 149 | Capacity sample upper validity bound | 1D | Single value / record | Sets the upper acceptable bound for the capacity sample consumed by the capacity path. |
| 150 | Capacity sample lower validity bound | 1D | Single value / record | Sets the lower acceptable bound for the capacity sample consumed by the capacity path. |
| 151 | Capacity qualification upper threshold | 1D | Single value / record | Sets the upper capacity threshold used by qualification logic; it is distinct from the diagnostic validity bound. |
| 152 | Capacity qualification lower threshold | 1D | Single value / record | Sets the lower capacity threshold used by qualification logic; it is distinct from the diagnostic validity bound. |

### Power and current limit configuration

Voltage-margin candidates, clamps, filtering and source selection.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 153 | Upper cell voltage reference for power limiting | 1D | Single value / record | Provides the upper cell-voltage reference used to calculate available voltage margin in the power-limit path. |
| 154 | Lower cell voltage reference for power limiting | 1D | Single value / record | Provides the lower cell-voltage reference used to calculate available voltage margin in the power-limit path. |
| 155 | Overall power candidate upper clamp | 1D | Single value / record | Sets the upper clamp applied to the overall power candidate before subsequent selection/use. |
| 156 | Overall power candidate lower clamp | 1D | Single value / record | Sets the lower clamp applied to the overall power candidate before subsequent selection/use. |
| 157 | Voltage margin current candidate minimum | 1D | Single value / record | Sets the minimum value allowed for a current candidate derived from cell-voltage margin. |
| 158 | Lower voltage margin current candidate upper clamp | 1D | Single value / record | Sets the upper clamp for the current candidate derived from lower cell-voltage margin. |
| 159 | Upper voltage margin power filter gain input | 1D | Single value / record | Provides the gain input used to filter the power candidate derived from upper voltage margin. |
| 160 | Lower voltage margin power filter gain input | 1D | Single value / record | Provides the gain input used to filter the power candidate derived from lower voltage margin. |
| 161 | Power limit source selector | 1D | Single value / record | Selects the firmware path supplying the power limit; this is a source selection rather than a direct power amount. |

### Cell configuration

Series-cell configuration selection.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 162 | Series cell count configuration selector | 1D | Single value / record | Selects the battery configuration used to obtain the series cell count. Increasing Ah capacity alone does not change the number of series cells. |

### Cell voltage monitor thresholds

Voltage status and qualification boundaries.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 163 | Maximum cell voltage condition threshold | 1D | Single value / record | Sets the maximum-cell-voltage comparison boundary for the associated condition logic. |
| 164 | Maximum cell voltage status threshold | 1D | Single value / record | Sets the maximum-cell-voltage boundary used to generate the corresponding status. |
| 165 | Minimum cell voltage status threshold | 1D | Single value / record | Sets the minimum-cell-voltage boundary used to generate the corresponding status. |
| 166 | Minimum cell voltage monitor threshold | 1D | Single value / record | Sets the minimum-cell-voltage comparison boundary for the associated monitor. |
| 167 | Minimum cell voltage qualification upper threshold | 1D | Single value / record | Sets the upper boundary in the minimum-cell-voltage qualification path. |
| 168 | Minimum cell voltage qualification lower threshold | 1D | Single value / record | Sets the lower boundary in the minimum-cell-voltage qualification path. |
| 169 | Minimum cell voltage monitor | 1D | Single value / record | Provides a voltage threshold used by a minimum-cell-voltage monitoring path; the label does not establish a complete fault-response policy. |
| 170 | Maximum cell voltage monitor | 1D | Single value / record | Provides a voltage threshold used by a maximum-cell-voltage monitoring path; the label does not establish a complete fault-response policy. |
| 171 | Cell voltage monitor auxiliary threshold | 1D | Single value / record | Provides an auxiliary voltage threshold used by cell-voltage monitoring logic. |

### Cell temperature monitor thresholds

Temperature status, qualification, hysteresis and override.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 172 | Maximum cell temperature status threshold | 1D | Single value / record | Sets the maximum-cell-temperature boundary used to generate the corresponding status. |
| 173 | Minimum cell temperature status threshold | 1D | Single value / record | Sets the minimum-cell-temperature boundary used to generate the corresponding status. |
| 174 | Maximum cell temperature qualification upper threshold | 1D | Single value / record | Sets the upper boundary in maximum-cell-temperature qualification logic. |
| 175 | Minimum cell temperature qualification lower threshold | 1D | Single value / record | Sets the lower boundary in minimum-cell-temperature qualification logic. |
| 176 | Maximum cell temperature hysteresis set threshold | 1D | Single value / record | Sets the temperature at which the maximum-temperature hysteresis condition is asserted. |
| 177 | Maximum cell temperature hysteresis clear threshold | 1D | Single value / record | Sets the temperature at which the maximum-temperature hysteresis condition clears, separate from the assertion threshold. |
| 178 | Estimated cell temperature override value | 1D | Single value / record | Provides an override value for the estimated cell-temperature path when that override is selected. |

### Pack current monitor thresholds

Pack-current condition and monitor boundaries.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 179 | Pack current condition threshold — first entry | 1D | Single value / record | Sets the pack-current comparison boundary for the first condition path represented by this name. |
| 180 | Pack current monitor | 1D | Single value / record | Provides the current threshold used by the associated pack-current monitoring path. |
| 181 | Pack current condition threshold — second entry | 1D | Single value / record | Sets the pack-current comparison boundary for the second condition path represented by this name; it is a separate XML entry. |

### SOC diagnostic configuration

Diagnostic SOC input validity and fallback.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 182 | Upper validity bound | 1D | Single value / record | Sets the upper acceptable value for the diagnostic SOC input before fallback is selected. This is a diagnostic validity boundary, not a universal SOC ceiling. |
| 183 | Lower validity bound | 1D | Single value / record | Sets the lower acceptable value for the diagnostic SOC input before fallback is selected. This is separate from the stored voltage-derived SOC bounds. |
| 184 | Fallback | 1D | Single value / record | Provides the replacement SOC value when the diagnostic SOC input fails its validity checks, and participates in initialization of that diagnostic representation. |

### State qualification counts

Persistence thresholds for decoded state latches.

The qualification counts use invocation-based thresholds rather than verified elapsed seconds. Most latch thresholds are five invocations per stored unit; the G/H delay-helper thresholds are fifty per stored unit. A zero threshold is not an enable/disable switch. Latch letters identify logic paths whose physical meanings remain unresolved.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 186 | State-latch A set qualification count | 1D | Single value / record | Sets the count threshold for qualifying latch A's set input when either of its source conditions is active. |
| 187 | State-latch B reset qualification count | 1D | Single value / record | Sets the count threshold used by latch B's reset qualification, considering its active-state and clear-condition counters. |
| 188 | State-latch C set qualification count | 1D | Single value / record | Sets the count threshold for qualifying latch C's set input from its source status. |
| 189 | State-latch D mode qualification count | 1D | Single value / record | Sets the mode-dependent count threshold contributing to latch D's set qualification, using the firmware's state-four/state-six conditions. |
| 190 | State-latch D auxiliary qualification count | 1D | Single value / record | Sets the auxiliary-condition count threshold contributing to latch D's set qualification. |
| 191 | State-latch D qualified output count | 1D | Single value / record | Sets the count threshold for qualifying latch D's output when latch D is active and the additional blocking condition is clear. |
| 192 | State-latch D active output count | 1D | Single value / record | Sets the count threshold for the output driven by latch D's active status. |
| 193 | State-latch E set qualification count | 1D | Single value / record | Sets the count threshold for latch E's set input, driven by either of the preceding latch-D output conditions. |
| 194 | State-latch E qualified output count | 1D | Single value / record | Sets the count threshold for latch E's qualified output when latch E is active and the additional blocking condition is clear. |
| 195 | State-latch E active output count | 1D | Single value / record | Sets the count threshold for the output driven by latch E's active status. |
| 196 | State four dwell qualification count | 1D | Single value / record | Sets the dwell-count threshold while the operating state equals four, before the corresponding condition is qualified. |
| 215 | Status-latch F qualification count in state A | 1D | Single value / record | Sets latch F's qualification count in selection state A, where the runtime state-selection flag is clear. |
| 216 | Status-latch F qualification count in state B | 1D | Single value / record | Sets latch F's qualification count in selection state B, where the runtime state-selection flag is set. It shares the counter and output with state A. |
| 217 | Status-latch G delay-helper threshold | 1D | Single value / record | Sets the count threshold passed to the delay helper for status latch G. The physical subsystem and scheduler period remain unresolved. |
| 218 | Status-latch H delay-helper threshold | 1D | Single value / record | Sets the count threshold passed to the delay helper for status latch H. The physical subsystem and scheduler period remain unresolved. |

### State qualification controls

Set/reset update gates for decoded state latches.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 197 | State-latch B set gate | 1D | Single value / record | Permits latch B's set input when nonzero and the other conditions pass. Zero blocks that set path; it does not unconditionally erase the latched state. |
| 198 | State-latch bank set gate | 1D | Single value / record | Permits the set inputs for the decoded latch bank when nonzero. Zero blocks those set paths while runtime reset inputs remain separate. |
| 219 | Status-latch F conditional set bypass gate | 1D | Single value / record | Bypasses one conditional block in latch F's set path when nonzero. The required latch input and the separate reset still apply. |
| 220 | Operating-state status-latch update gate | 1D | Single value / record | Enables this routine's operating-state-dependent set and reset updates when nonzero. Zero prevents both updates from this routine. |

### Channel status diagnostics

Grouped status and mismatch qualification.

The eighty-channel status array is grouped into ten banks of eight. These are status-array indices; a physical sensor or fault assignment has not been established for each group. Assertion and recovery thresholds use invocation counts, and the startup/mismatch thresholds use five invocations per stored unit.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 199 | Eight-channel monitor startup qualification count | 1D | Single value / record | Sets the startup qualification threshold that must pass before the eighty-channel status monitor proceeds. |
| 200 | Channel status assertion qualification count | 1D | Single value / record | Sets the count threshold for asserting each channel's status condition. The saturated byte counter cannot exceed a threshold of 255, so that value suppresses this assertion path. |
| 201 | Channel status recovery qualification count | 1D | Single value / record | Sets the count threshold for qualifying recovery when each channel's source status is clear. A threshold of 255 cannot be exceeded by its saturated byte counter. |
| 202 | Eight-channel group 1 status monitor gate | 1D | Single value / record | Permits assertion and all-clear recovery qualification for status-array channels 1–8 when nonzero and runtime conditions pass. Zero blocks these group paths; the separate reset remains available. |
| 203 | Eight-channel group 2 status monitor gate | 1D | Single value / record | Permits assertion and all-clear recovery qualification for status-array channels 9–16 when nonzero and runtime conditions pass. Zero blocks these group paths; the separate reset remains available. |
| 204 | Eight-channel group 3 status monitor gate | 1D | Single value / record | Permits assertion and all-clear recovery qualification for status-array channels 17–24 when nonzero and runtime conditions pass. Zero blocks these group paths; the separate reset remains available. |
| 205 | Eight-channel group 4 status monitor gate | 1D | Single value / record | Permits assertion and all-clear recovery qualification for status-array channels 25–32 when nonzero and runtime conditions pass. Zero blocks these group paths; the separate reset remains available. |
| 206 | Eight-channel group 5 status monitor gate | 1D | Single value / record | Permits assertion and all-clear recovery qualification for status-array channels 33–40 when nonzero and runtime conditions pass. Zero blocks these group paths; the separate reset remains available. |
| 207 | Eight-channel group 6 status monitor gate | 1D | Single value / record | Permits assertion and all-clear recovery qualification for status-array channels 41–48 when nonzero and runtime conditions pass. Zero blocks these group paths; the separate reset remains available. |
| 208 | Eight-channel group 7 status monitor gate | 1D | Single value / record | Permits assertion and all-clear recovery qualification for status-array channels 49–56 when nonzero and runtime conditions pass. Zero blocks these group paths; the separate reset remains available. |
| 209 | Eight-channel group 8 status monitor gate | 1D | Single value / record | Permits assertion and all-clear recovery qualification for status-array channels 57–64 when nonzero and runtime conditions pass. Zero blocks these group paths; the separate reset remains available. |
| 210 | Eight-channel group 9 status monitor gate | 1D | Single value / record | Permits assertion and all-clear recovery qualification for status-array channels 65–72 when nonzero and runtime conditions pass. Zero blocks these group paths; the separate reset remains available. |
| 211 | Eight-channel group 10 status monitor gate | 1D | Single value / record | Permits assertion and all-clear recovery qualification for status-array channels 73–80 when nonzero and runtime conditions pass. Zero blocks these group paths; the separate reset remains available. |
| 212 | Ten-channel mismatch startup qualification count | 1D | Single value / record | Sets the startup qualification threshold before the ten-channel mismatch monitor proceeds. |
| 213 | Ten-channel mismatch qualification count | 1D | Single value / record | Sets the persistence-count threshold for ten channel statuses compared with the expected state. Any qualified mismatch contributes to the monitor's diagnostic result. |
| 214 | Ten-channel mismatch monitor gate | 1D | Single value / record | Permits mismatch monitoring when nonzero and the other interlocks pass. Zero blocks monitoring and clears the comparison counters through their runtime logic. |

### SOC validity and selection

Stored voltage-derived SOC validation and estimator selection.

The stored-SOC bounds are inclusive percentages. Each voltage exclusion interval includes its lower edge and excludes its upper edge; equal edges produce an empty band. The ceiling and bands select an SOC estimate and are not independent charge/discharge protection limits.

| # | Definition | Type | Inputs / index | Purpose |
|---:|---|---|---|---|
| 222 | Stored voltage-derived SOC upper validity bound | 1D | Single value / record | Sets the inclusive upper validity bound for the paired stored voltage-derived SOC inputs. Invalid inputs use the paired SOC default factor. |
| 223 | Stored voltage-derived SOC lower validity bound | 1D | Single value / record | Sets the inclusive lower validity bound for the paired stored voltage-derived SOC inputs. This percentage boundary is separate from Ah capacity validation. |
| 224 | Voltage-derived SOC selection ceiling | 1D | Single value / record | Allows interpolated voltage-derived SOC only when the voltage input is strictly below this ceiling and the exclusion bands also permit it. Otherwise the validated stored SOC is selected. |
| 225 | Voltage-derived SOC exclusion band 1 lower edge | 1D | Single value / record | Sets the inclusive lower voltage edge of exclusion band 1, inside which validated stored SOC is selected instead of interpolated SOC. |
| 226 | Voltage-derived SOC exclusion band 1 upper edge | 1D | Single value / record | Sets the exclusive upper voltage edge of exclusion band 1. Equal lower and upper edges make the band empty. |
| 227 | Voltage-derived SOC exclusion band 2 lower edge | 1D | Single value / record | Sets the inclusive lower voltage edge of exclusion band 2, inside which validated stored SOC is selected instead of interpolated SOC. |
| 228 | Voltage-derived SOC exclusion band 2 upper edge | 1D | Single value / record | Sets the exclusive upper voltage edge of exclusion band 2. Equal lower and upper edges make the band empty. |
| 229 | Voltage-derived SOC exclusion band 3 lower edge | 1D | Single value / record | Sets the inclusive lower voltage edge of exclusion band 3, inside which validated stored SOC is selected instead of interpolated SOC. |
| 230 | Voltage-derived SOC exclusion band 3 upper edge | 1D | Single value / record | Sets the exclusive upper voltage edge of exclusion band 3. Equal lower and upper edges make the band empty. |

## Scope of the descriptions

These descriptions explain the roles represented by the current definition and the decoded consumer paths. Inherited labels with unresolved physical meaning are kept conservative. Firmware identification, CAN routing, diagnostic defaults and encoded trouble-code records are documented as reference/configuration data rather than presented as general battery-performance controls.

No universal replacement-pack values, verified service reset procedure or vehicle-tested calibration is supplied by this reference. The purpose inventory is complete for the current XML; firmware-wide definition coverage remains in progress.
