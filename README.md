![https://www.microchip.com/](assets/microchip.png)
# Template MPLAB X Project for SensiML
# Contents
* [Overview](#overview)
* [Knowledge Pack Deployment](#knowledge-pack-deployment)
* [Knowledge Pack Integration](#knowledge-pack-integration)
  * [Binary Format](#binary-format)
  * [Library Format](#library-format)
  * [Source Format](#source-format)
* [Firmware Operation](#firmware-operation)
* [Visualization with SensiML Open Gateway](#visualization-with-sensiml-open-gateway)

<div style="page-break-after: always;"></div>

# Overview
This repository contains an MPLAB X project that can be used as a starting point for running any SensiML Knowledge Pack on the SAMD21 Machine Learning Evaluation Kit ([BMI160](https://www.microchip.com/developmenttools/ProductDetails/EV45Y33A) or [ICM42688](https://www.microchip.com/DevelopmentTools/ProductDetails/PartNO/EV18H79A) variant). Read on for instructions on how to go from a SensiML Knowledge Pack deployment to a full working MPLAB X project.

| ![ml eval kits](assets/mlkits.png) |
| :--: |
| *Microchip SAMD21 Machine Learning Evaluation Kits* |

## Hardware Used
* SAMD21 Machine Learning Evaluation Kit with Bosch BMI160 IMU ([EV45Y33A](https://www.microchip.com/developmenttools/ProductDetails/EV45Y33A))
* SAMD21 Machine Learning Evaluation Kit with TDK ICM42688 IMU ([EV18H79A](https://www.microchip.com/developmenttools/ProductDetails/EV18H79A))

## Software Used
* MPLAB® X IDE (https://microchip.com/mplab/mplab-x-ide)
* MPLAB® XC32 compiler (https://microchip.com/mplab/compilers)
* MPLAB® Harmony 3 (https://www.microchip.com/harmony)
* SensiML Analytics Toolkit (https://sensiml.com/download/)

## Related Documentation
* ATSAMD21G18 [Product Family Page](https://www.microchip.com/wwwproducts/en/ATSAMD21G18)
* SAM-IoT WG Development Board [Product Details](https://www.microchip.com/developmenttools/ProductDetails/EV75S95A)

<div style="page-break-after: always;"></div>

# Knowledge Pack Deployment
To deploy a knowledge pack for the SAMD21 ML Eval Kit:

1. Open up your SensiML project in the [Analytics Studio](https://app.sensiml.cloud/) and navigate to the *Download Model* tab.
2. Select `Microchip SAMD21 ML Eval Kit` from the *HW Platform* options.
3. Select one of *Binary*, *Library* or *Source* from the *Format* options. *Note: source format is only available for upper tier customers*.
4. Select the appropriate sensor configuration for your project from the *Data Source* options. Note that this configuration should match the one you used to capture the data your model was trained with.
5. Click the *Download* button to download the model.

| ![https://www.microchip.com/](assets/deployment.jpeg) |
| :--: |
| Deployment from the Analytics Studio |

<div style="page-break-after: always;"></div>

# Knowledge Pack Integration
## Binary Format
If you deployed a knowledge pack in the *Binary* format, the archive should contain the binary file which can be written directly to the SAMD21.

## Library Format
If you deployed a knowledge pack in the *Library* format, the archive should contain a complete, ready to compile, MPLAB X project. Follow the steps below to compile your project:

1. In MPLAB X, open up the .X project folder under the `firmware` folder of the knowledge pack
2. Select the *Project Configuration* option in the MPLAB X toolbar according to which sensor you're using.
   | ![https://www.microchip.com/](assets/project-configuration.png) |
   | :--: |
   | Set the Project Configuration according to sensor |

Your project should now be ready to compile.

## Source Format
If you deployed a knowledge pack in the *Source* format there are a few manual steps to add the required sensiml files to the MPLAB X project:

1. In MPLAB X, open up the .X project folder under the `firmware` folder of this repository.
2. Select the *Project Configuration* option in the MPLAB X toolbar according to which sensor you're using.
   | ![https://www.microchip.com/](assets/project-configuration.png) |
   | :--: |
   | Set the Project Configuration according to sensor |
3. In the *Projects* pane, right click the *Header Files* folder under the template project and select *Add Existing Items from Folders* from the options.
   | ![https://www.microchip.com/](assets/add_header_files_1.png) |
   | :--: |
   | Adding knowledge pack header files to your project |
4. In the resulting window that opens, click the *Add Folder* button.
5. In the new dialog, select *Header Files* from the *Files of Type* dropdown, then navigate to the template project's `firmware` directory and select the `knowledgepack` directory. Click the *Select* button to add the directory.
   | ![https://www.microchip.com/](assets/add_header_files_2.png) |
   | :--: |
   | Recursively add header files (.h) under the `knowledgepack` folder |
6. Back in the *Add Files* window, click the *Add* button to finish adding the necessary header files and close the dialog.
7. Right click the *Source Files* folder in the *Projects* pane and select *Add Existing Items from Folders* from the menu. Repeat steps 4-6 to add all source files (.c files) from the `knowledgepack` folder.

Your Knowledge Pack is now integrated into your MPLAB X project. You should be able to compile your project and flash it onto the SAMD21.

<div style="page-break-after: always;"></div>

# Firmware Operation
The firmware behavior can be summarized as operating in one of three distinct states as reflected by the onboard LEDs and described in the table below:

| State |	LED Behavior |	Description |
| --- | --- | --- |
| Error |	Red (ERROR) LED lit |	Fatal error. (Do you have the correct sensor plugged in?). |
| Buffer Overflow |	Yellow (DATA) and Red (ERROR) LED lit for 5 seconds	| Processing is not able to keep up with real-time; data buffer has been reset. |
| Running | Yellow (DATA) LED flashing slowly |	Firmware is running normally. |

When operating normally, the firmware prints the classification prediction (classification ID number) and the generated feature vector for each sample window over the UART port. To read the UART port use a terminal emulator of your choice (e.g., MPLAB Data Visualizer's integrated terminal tool) with the following settings:

- Baudrate 115200
- Data bits 8
- Stop bits 1
- Parity None

A sample of the terminal output is shown in the figure below.

| ![Terminal output](assets/terminal-output.png) |
| :--: |
| UART Terminal Output |

Note that by default the feature vector will not be output. You can enable this behavior by setting `write_features=1` in `sml_output.c`.

<div style="page-break-after: always;"></div>

# Visualization with SensiML Open Gateway
In addition to reading the text output of the firmware with a terminal emulator, the output may be visualized with the SensiML [Open Gateway application](https://github.com/sensiml/open-gateway). Follow the below instructions to get started:

1. Open a terminal and change to the directory where you've checked out this repository.
1. Clone the open-gateway repository and install the dependencies:
    > `git clone https://github.com/sensiml/open-gateway`\
    > `pip install -r open-gateway/requirements.txt`
2. Change the baudrate (`BAUD_RATE` variable) in `open-gateway/config.py` to 115200
3. Change to the open-gateway directory and run the open-gateway application, passing in the knowledge pack model.json description file:
   > `cd open-gateway`\
   > `python app.py -m $(find ../firmware/knowledgepack/ -name model.json)`
4. Connect to the SAMD21 board in the gateway application:
   * Select the `Recognition` device mode.
   * Select `Serial` connection type, and enter the UART address (e.g. COM4) in the `Device ID` field.
   * Click `Connect To Device`.
6. Switch to the `Test Mode` tab and click `Start Stream`.
