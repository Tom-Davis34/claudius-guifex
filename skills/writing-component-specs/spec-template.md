# <Component> — Spec

## States
| id       | when                                   |
|----------|----------------------------------------|
| default  | <the resting state>                    |
| <id>     | <observable condition>                 |

## Stories
### US-1: <imperative title>
Given <starting state>
When <the user action>
Then <one observable outcome>
And <optional second observable outcome>

## Responsive
<!-- Universal invariants — keep these; delete only with a stated reason -->
- No horizontal overflow at any width >= 320px
- Images/media never exceed their container
- Text truncates or wraps; never clips or overlaps
<!-- Component-specific thresholds — add yours, or delete this line -->
- <e.g. action row wraps when container < 480px>
