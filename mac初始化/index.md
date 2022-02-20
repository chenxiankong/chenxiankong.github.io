# Mac初始化


# 必备好软

# ShowyEege

[ShowyEdge (pqrs.org)](https://showyedge.pqrs.org/)

便捷显示当前输入法

## Karabiner-Elements

键盘改键神器

[Karabiner-Elements (pqrs.org)](https://karabiner-elements.pqrs.org/)

复杂配置文件目录:

```
~/.config/karabiner/
```

附上我的配置文件

```json
{
  "title": "CapsLock / Control / Tab",
  "rules": [
    {
      "description": "alt",
      "manipulators": [
        {
          "from": {
            "key_code": "left_command",
            "modifiers": {
              "optional": [
                "any"
              ]
            }
          },
          "parameters": {
            "basic.to_if_held_down_threshold_milliseconds": 30
          },
          "to_if_alone": [
            {
              "key_code": "f19"
            }
          ],
          "to_if_held_down": [
            {
              "key_code": "left_command"
            }
          ],
          "type": "basic"
        }
      ]
    },
    {
      "description": "caps_lock",
      "manipulators": [
        {
          "from": {
            "key_code": "caps_lock",
            "modifiers": {
              "optional": [
                "any"
              ]
            }
          },
          "parameters": {
            "basic.to_if_held_down_threshold_milliseconds": 30
          },
          "to_if_alone": [
            {
              "key_code": "f18"
            }
          ],
          "to_if_held_down": [
            {
              "key_code": "left_option"
            }
          ],
          "type": "basic"
        }
      ]
    },
    {
      "description": "tab+alt",
      "manipulators": [
        {
          "from": {
            "key_code": "tab",
            "modifiers": {
              "mandatory": [
                "left_command"
              ]
            }
          },
          "to": [
            {
              "key_code": "tab",
              "modifiers": {
                "mandatory": [
                  "left_command"
                ]
              }
            }
          ],
          "type": "basic"
        }
      ]
    },
    {
      "description": "tab",
      "manipulators": [
        {
          "from": {
            "key_code": "tab",
            "modifiers": {
              "optional": [
                "any"
              ]
            }
          },
          "parameters": {
            "basic.to_if_held_down_threshold_milliseconds": 30
          },
          "to_if_alone": [
            {
              "key_code": "tab"
            }
          ],
          "to_if_held_down": [
            {
              "key_code": "left_option"
            }
          ],
          "type": "basic"
        }
      ]
    },
    {
      "description": "left_option+c -> left_control+c",
      "manipulators": [
        {
          "from": {
            "key_code": "c",
            "modifiers": {
              "mandatory": [
                "left_option"
              ]
            }
          },
          "to": [
            {
              "key_code": "c",
              "modifiers": [
                "left_control"
              ]
            }
          ],
          "type": "basic"
        }
      ]
    }
  ]
}



```



## 自动切换输入法lite

appStore自行下载


