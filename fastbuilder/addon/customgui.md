[Back](../)
<br>

**You need the CustomGUI addon for anything on this page to work.**

Table of contents:
- [Actions](#actions)
- [List of options in each config section](#list-of-options-in-each-config-section)
- [Example](#example)

## Actions

| Action | Description | Example
| --- | --- | ---
| `mode.<MODE>` | make the player switch to a different mode | `mode.short`
| `cmd.<COMMAND>` | make the player execute a command | `cmd.fb leave`
| `block.<BLOCK>.<PRICE>` | buying a block, if already brought select | `block.WOOL:0.200`
| `pick.<PICKAXE>` | same as selecting a pick |` pick.DIAMOND_PICKAXE`
| `msg.<TEXT>` | send player a message | `msg.&aHello there!`
| `gui.<GUI>` | open another gui | `gui.blocks`

## List of options in each config section


| Name | Description | Optional
| --- | --- | ---
| Item | The item in the GUI | No
| Name | The display name of the item | Yes
| Actions | A list of actions that will be executed when you click the item | Yes
| Lore | The lore displayed on the item | Yes
| Amount | The amount of item shown | Yes

## Example

```yml
# Overwrite: Left is the default gui and right is any gui you made
overwrite:
  SETTINGS: "[none]"
  PICKAXES: "[none]"
  BLOCKS: "[none]"
  MODES_SELECTOR: "modes"
gui:
  modes: # GUI Name, Can be anyting
    name: "Modes" # Name of the GUI
    size: 2 # How big the GUI is
    items: # Items in the GUI
      0: # Slot of the item
        name: "&cTest"
        item: "WOOL:14"
        lore: "&e&lClick to switch"
        actions:
          - "mode.test"
      1: # Slot of the item
        name: "&aOpen another gui"
        item: "WOOL:14"
        actions:
          - "gui.test"
  test:
    name: "Test GUI"
    size: 1
    items:
      0:
        name: "&4Go back"
        item: "BARRIER:0"
        actions:
          - "gui.modes"
```