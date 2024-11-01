# ESPHome Configuration for Smart Hut Plugs

This repository stores the configuration for the ESPHome based firmware of ESPC2-02 based plugs from [Smart Hut](https://thesmarthut.com/) available pre-flashed [here](https://thesmarthut.com/products/power-monitoring-smart-plug-preflashed-preconfigured) 

## Pin Outs
These are the pinouts for our plugs if you'd like to build your own configuration or use them with alternative firmware.

### BL0937
Used for energy monitoring

| Pin | function |
|----|----|
| GPIO5 | SEL |
| GPIO7 | CF |
| GPIO3 | CF1|

### Plug

| Pin | function |
|----|----|
| GPIO20 | Button |
| GPIO6 | LED |
| GPIO4 | Relay |
