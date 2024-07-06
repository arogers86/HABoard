# HABoard

HABoard is a Home Assistant dashboard inspired by DAKBoard, providing an interactive experience with additional features.

![Dashboard Example](images/HomeAssistant%20Board.png)

## Features
- Customizable Lovelace dashboard
- Centralized and capped content alignment
- Interactive elements for Home Assistant
- Dynamic photo updates every 5 minutes from a shared iCloud album

## Dependencies

HABoard relies on the following custom components:

1. **card_mod**:
    - Repository: [lovelace-card-mod](https://github.com/thomasloven/lovelace-card-mod)
2. **layout-card**:
    - Repository: [layout-card](https://github.com/thomasloven/lovelace-layout-card)
3. **wallpanel**:
    - Repository: [lovelace-wallpanel](https://github.com/j-a-n/lovelace-wallpanel)
4. **digital-clock**:
    - Repository: [lovelace-digital-clock](https://github.com/wassy92x/lovelace-digital-clock)
5. **refreshable-picture-card**:
    - Repository: [refreshable-picture-card](https://github.com/dimagoltsman/refreshable-picture-card)
6. **week-planner-card**:
    - Repository: [week-planner-card](https://github.com/FamousWolf/week-planner-card)

## Weekly Planner Config
The weekly planner has been heavily modified by use of [card-mod](https://github.com/thomasloven/lovelace-card-mod).
Summary of modifications:
1. **Weekdays are shown above first row of days**
2. **Today is highlighted with a red circle**
3. **Past events darkened**
3. **General font changes**
4. **Padding and margin changes**

```yaml
card_mod:
  style:
    .: |
      ha-card {
        margin-top: 0px;
        font-size: 9px !important;
        overflow: hidden;
        --days-spacing: 10px;
        --event-padding: 2px !important;
        --events-margin-top: 1px;
        --event-border-width: 2px;
      }
      .event.past {
        opacity: 0.3;
      }
      .none {
        background-color: transparent !important;
      }
      .container .day {
        width: calc((100% - 6 * var(--days-spacing)) / 7) !important;
      }
      .container .day .date {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
      }
      .container .day .date .number {
        order: 2;
        height: 40px;
        width: 40px;
        padding: 8px;
        margin-top: 5px;
        display: table-cell;
        vertical-align: middle;
        text-align: center;
        font-weight: 300;
        font-family: "Roboto", sans-serif;
      }
      .container .day .date .text {
        order: 1;
        font-weight: 500;
        font-family: "Roboto", sans-serif;
        font-size: 18px !important;
      }
      .today .date .number {
        background-color: #ff0000 !important;
        border-radius: 50%;
      }
      .container .day:nth-child(n+8) .date .text {
        display: none;
      }
```


## Dynamic Photo Updates
The photo displayed on HABoard is replaced every 5 minutes. This photo is downloaded from a shared iCloud album using a Python script from the [iclouder repository](https://github.com/arogers86/iclouder).

### Setup
1. Place the `iclouder.py` script in your Home Assistant `python_scripts` directory.
2. Add the following `shell_command` to your `configuration.yaml` file:
    ```yaml
    shell_command:
      icloud_photo_download: python3 /config/python_scripts/iclouder.py <Your Token> --destination www/images/ --single --filename icloud_photo.jpg --ignore 50
    ```
3. Create a Home Assistant automation to call this shell command every 5 minutes:
    ```yaml
    automation:
      - alias: Download iCloud Photo
        trigger:
          platform: time_pattern
          minutes: "/5"
        action:
          service: shell_command.icloud_photo_download
    ```

### Dashboard Configuration
The photo card on the dashboard refreshes the image every 3 seconds. When a new photo is downloaded, it will be updated on the dashboard accordingly.

Example configuration for the photo card:

```yaml
- type: custom:refreshable-picture-card
  url: /local/images/icloud_photo.jpg
  noMargin: true
  refresh_interval: 3
```