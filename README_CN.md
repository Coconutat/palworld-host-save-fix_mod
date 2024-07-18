# Palworld 主机存档修复

***
# 语言:  
[English](README.md) | **简体中文**  
**注意：部分翻译可能使用ChatGPT辅助翻译。**  
***
这个Fork存在的意义是什么？  
参见此：[Note](Note.md)  
***
> ### :warning: 此工具是实验性的。小心数据丢失，*务必*进行备份。:warning:

修复了一个迫使玩家在已有存档时创建新角色的bug。

*注意：如果你有一个高级的合作地图，角色等级在30-40级以上，并且无法让修复程序正常工作，请提出问题并将存档发送给我。我正在进行一些重大且更完整的修改，但需要中后期的存档来确保一切正常，并请人确认它的正确性，之后我会发布。*

## 目录

- [Palworld 主机存档修复](#palworld-主机存档修复)
- [语言:](#语言)
  - [目录](#目录)
  - [摘要](#摘要)
  - [使用方法](#使用方法)
  - [迁移示例](#迁移示例)
    - [如何将合作存档迁移到Windows专用服务器](#如何将合作存档迁移到windows专用服务器)
    - [如何将Windows/Linux专用服务器存档迁移到Linux/Windows专用服务器](#如何将windowslinux专用服务器存档迁移到linuxwindows专用服务器)
    - [如何将Windows专用服务器存档迁移到合作模式](#如何将windows专用服务器存档迁移到合作模式)
  - [查找玩家GUID](#查找玩家guid)
  - [已知的bug](#已知的bug)
    - [\[公会bug\]](#公会bug)
    - [\[Pal bug\]](#pal-bug)
    - [\[观景笼bug\]](#观景笼bug)
    - [\[左键bug\]](#左键bug)
    - [感谢 cheahjs 的非常有用的脚本，帮助我完成了这个修复！](#感谢-cheahjs-的非常有用的脚本帮助我完成了这个修复)
    - [感谢任何测试和解决bug的帮助。](#感谢任何测试和解决bug的帮助)

## 摘要 

Palworld的存档文件因服务器类型的不同而有所不同。合作模式、Windows专用服务器、Linux专用服务器、SteamCMD专用服务器，这些都是不同类型的Palworld服务器，如果尝试将一个服务器的存档文件迁移到另一个类型的服务器，就会遇到一个玩家存档bug，迫使你创建一个新角色。

例如：
- 将Windows合作存档迁移到Windows专用服务器时，会迫使主机创建新角色并丢失存档。
- 将Windows专用服务器存档迁移到Linux专用服务器时，会迫使所有玩家创建新角色并丢失存档。
- 将Linux专用服务器存档迁移到Windows专用服务器时，会迫使所有玩家创建新角色并丢失存档。
- 将Windows合作存档迁移到Linux专用服务器时，会迫使所有玩家创建新角色并丢失存档。
- 等等。

该bug的发生是因为玩家通过他们的GUID来识别和关联他们的存档。这些不同类型的服务器生成玩家GUID的方式不同，因此当玩家加入时，服务器会生成一个新的GUID，而这个GUID与旧存档的GUID不匹配，因此服务器不知道玩家已经有了存档。

为了修复这个bug，我们编写了一个脚本，该脚本会将新服务器上的玩家GUID应用到旧服务器上的玩家存档中，以便新服务器使用旧服务器上的玩家存档。

## 使用方法

依赖项:
- Python >=3.10
- 使用 `python -m pip install palworld-save-tools==0.23.1` 安装 [Palworld Save Tools v0.23.1](https://github.com/cheahjs/palworld-save-tools)
- ~~使用 `python -m pip install palworld-save-tools==0.17.1` 安装 [Palworld Save Tools v0.17.1](https://github.com/cheahjs/palworld-save-tools)~~
- 使用 `git clone https://github.com/xNul/palworld-host-save-fix` 克隆存储库

使用GUI:
- 在文件夹中打开命令提示符
- 运行命令 `python gui.py`
- 浏览你的存档文件夹
- 从下拉菜单中选择要覆盖的新角色和要修复/保留的旧角色
- 如果需要，启用公会修复
- 点击按钮运行命令
- 阅读命令提示符中的警告并按回车键

命令:    
```
python fix_host_save.py <save_path> <new_guid> <old_guid> <guild_fix>
```      
`<save_path>` - 存档文件夹路径    
`<new_guid>` - 新服务器上玩家的GUID    
`<old_guid>` - 旧服务器上玩家的GUID    
`<guild_fix>` - True 或 False。应用 [\[公会bug\]](#公会bug) 的修复。

示例:    
```
python fix_host_save.py "C:\Users\John\Desktop\my_temporary_folder\2E85FD38BAA792EB1D4C09386F3A3CDA" 6E80B1A6000000000000000000000000 00000000000000000000000000000001 False
```

## 迁移示例

### 如何将合作存档迁移到Windows专用服务器
*只有我的合作主机无法在专用服务器上使用他们的角色。*

前提条件:
- 安装[上面的](#使用方法)依赖项。
- 专用服务器已安装并运行，你能够加入它。
- 在迁移存档之前，按照[下面](#公会bug)的 \[公会bug\] 解决方法进行操作。
- 如果你有一个观景笼，请按照[下面](#观景笼bug)的 \[观景笼bug\] 解决方法在迁移存档之前进行操作。

步骤:
1. 将你想要的存档文件夹从 `C:\Users\<username>\AppData\Local\Pal\Saved\SaveGames\<random_numbers>` 复制到你的专用服务器目录 `PalServer\Pal\Saved\SaveGames\0`。
2. 在新的专用服务器的 `PalServer\Pal\Saved\Config\WindowsServer\GameUserSettings.ini` 文件中，将 `DedicatedServerName` 更改为与你的存档文件夹名称匹配。例如，如果你的存档文件夹名称为 `2E85FD38BAA792EB1D4C09386F3A3CDA`，则 `DedicatedServerName` 更改为 `DedicatedServerName=2E85FD38BAA792EB1D4C09386F3A3CDA`。
3. 删除 `PalServer\Pal\Saved\SaveGames\0\<your_save_here>\WorldOption.sav` 以允许修改 `PalWorldSettings.ini`。玩家将不得不再次选择他们的重生点，但据我所知，其他没有影响。
4. 确认你可以连接到专用服务器上的存档，并确保这是你想要的世界。你可以用一个不属于合作主机的角色连接到专用服务器并检查世界。
5. 然后，合作主机必须在专用服务器上创建一个新角色。一个新的 `.sav` 文件应出现在 `PalServer\Pal\Saved\SaveGames\0\<your_save_here>\Players` 中。
6. 该新的 `.sav` 文件的名称是合作主机的新GUID。我们需要合作主机的新GUID才能使脚本正常工作。
7. 关闭服务器，然后将整个存档文件夹从专用服务器目录 `PalServer\Pal\Saved\SaveGames\0\<your_save_here>`（必须是包含合作主机新角色的存档文件夹！）复制到一个临时文件夹，并记住临时文件夹的路径，因为运行脚本时需要它。
8. **备份你的存档文件夹！** 这是一个实验性脚本，已知存在bug，因此请始终保留存档文件夹的备份副本。
9. 使用[使用方法部分](#使用方法)中的命令和收集到的信息运行脚本，使用 `00000000000000000000000000000001` 作为合作主机的旧GUID，并确保将 `<guild_fix>` 设置为 `False`。
10. 将存档文件夹从临时文件夹复制回专用服务器。将你在专用服务器中的存档文件夹移动到其他地方或重命名为其他名称。
11. 重新启动服务器，让合作主机加入服务器并使用他们修复后的角色。
12. 如果在游戏5分钟后，你的Pals不攻击或在基地不工作，按照[Pal bug解决方法](#pal-bug)进行修复。

### 如何将Windows/Linux专用服务器存档迁移到Linux/Windows专用服务器
*没有玩家，包括合作主机，能够在专用服务器上使用他们的角色。*

注意：此方法依赖于\[公会bug\]修复，尽管该修复本身存在bug，因为在这个迁移过程中，每个玩家都会失去他们的角色数据，他们都必须被修复，所以没有“好”的角色可以为其他玩家保留公会，就像上面的合作到专用服务器迁移过程一样。\[公会bug\]

修复的进展正在进行中，希望它很快能完全修复。

前提条件:
- 安装[上面的](#使用方法)依赖项。
- 新的专用服务器已安装并运行，你能够加入它。

步骤:
1. 将存档文件夹从旧的专用服务器复制到新的专用服务器。
2. 在新的专用服务器的 `PalServer\Pal\Saved\Config\WindowsorLinuxServer\GameUserSettings.ini` 文件中，将 `DedicatedServerName` 更改为与你的存档文件夹名称匹配。例如，如果你的存档文件夹名称为 `2E85FD38BAA792EB1D4C09386F3A3CDA`，则 `DedicatedServerName` 更改为 `DedicatedServerName=2E85FD38BAA792EB1D4C09386F3A3CDA`。
3. 启动新服务器，让每个玩家创建一个新角色。当玩家创建新角色时，一个新的 `.sav` 文件将出现在 `PalServer\Pal\Saved\SaveGames\0\<your_save_here>\Players` 中。新 `.sav` 文件的名称就是玩家的新GUID。确保记录所有旧GUID、新GUID及其所属的玩家。
4. 关闭服务器，然后将整个存档文件夹从新服务器目录 `PalServer\Pal\Saved\SaveGames\0\<your_save_here>`（必须是包含所有新角色的存档文件夹！）复制到一个临时文件夹，并记住临时文件夹的路径，因为运行脚本时需要它。
5. **备份你的存档文件夹！** 这是一个实验性脚本，已知存在bug，因此请始终保留存档文件夹的备份副本。
6. 对每个玩家的新旧GUID对，使用[使用方法部分](#使用方法)中的命令运行脚本，并确保将 `<guild_fix>` 设置为 `True`。
7. 将存档文件夹从临时文件夹复制回专用服务器。将你在专用服务器中的存档文件夹移动到其他地方或重命名为其他名称。
8. 重新启动服务器，让每个玩家使用他们修复后的角色加入服务器。
9. 如果在游戏5分钟后，某个玩家的Pals不为他们攻击或在基地不工作，让他们按照[Pal bug解决方法](#pal-bug)进行修复。

### 如何将Windows专用服务器存档迁移到合作模式

1. 将存档从专用服务器复制到一个临时文件夹。
2. 开始一个新的合作游戏并创建一个新角色，将新角色的 `C:\Users\<username>\AppData\Local\Pal\Saved\SaveGames\<random_numbers>\Players\00000000000000000000000000000001.sav` 复制到 `<your_temporary_folder>\<your_save_here>\Players`。
3. 使用 `00000000000000000000000000000001` 作为 `<new_guid>`，并将你想作为主机的角色作为 `<old_guid>` 运行脚本。
4. 完成后，可以将存档文件夹从临时文件夹复制到 `C:\Users\<username>\AppData\Local\Pal\Saved\SaveGames`。
5. 启动游戏，应该可以正常游戏，朋友们可以毫无问题地加入，因为他们的GUID保持不变。

## 查找玩家GUID

如果你在确定哪个GUID属于哪个玩家时遇到问题，可以尝试使用以下步骤：

1. 在旧服务器的 `PalServer\Pal\Saved\Config\WindowsorLinuxServer\PalWorldSettings.ini` 文件中设置管理员密码。
2. 连接到旧服务器，打开聊天，输入 `/adminpassword <your_admin_password>`。
3. 打开聊天并运行 `/showplayers`（或者你可以按 `esc` 键并进入选项页面，在输入管理员密码后查看并复制自己的ID）。
4. 记录每个玩家的 `playeruid` 字段。
5. 使用一个工具将 `playeruid` 数字转换为8字符十六进制GUID前缀。例如，你可以运行
```bash
python -c "print(format(<your_player_id_number>, '08x'))"
```
6. 该命令的输出就是玩家GUID的前缀（即查找以该输出开头的 `.sav` 文件）。
7. 如果需要，对新服务器重复这些步骤。

## 已知的bug

### \[公会bug\]

细节：修复角色后，公会成员资格无法正常工作。这很可能是因为在从旧存档迁移到新存档时，某些公会配置被遗漏了。

解决方法：\[仅限合作\] 在合作模式中，在迁移存档之前，将公会所有权从合作主机的角色转移到另一个角色，并让合作主机的角色退出公会。可以完全修复该问题。如果所有玩家都丢失了他们的存档数据，因为没有正常工作的玩家来保留公会，这种方法无效。

### \[Pal bug\]

细节：玩家拥有的Pals在基地不做任何事情。这很可能是因为这些Pals没有注册到正确的公会。

解决方法：在新服务器上，修复存档后，让每个玩家的角色进入他们的基地，把他们拥有的每个Pal（包括基地工人）都放下并捡起。这可以通过在“队伍”菜单中使用“放下”按钮完成。这将重新注册这些Pals到正确的公会，并完全修复该问题。

### \[观景笼bug\]

细节：观景笼在专用服务器上[不被正式支持](https://tech.palworldgame.com/dedicated-server-guide#qa)，因此如果你在合作模式中建造了一个观景笼，需要在将存档迁移到专用服务器之前将其移除。

解决方法：\[仅限合作\] 如果你建造了一个观景笼，需要在将存档迁移到专用服务器之前将其移除。

### \[左键bug\]

细节：应用修复后，有些玩家会遇到一个bug，即无法按住左键进行攻击。似乎只有在没有进行\[公会bug\]解决方法时才会发生，但我不确定。

解决方法：如果你离开并重新加入公会，问题会消失。感谢 [/u/skalibran](https://www.reddit.com/r/Palworld/comments/19axeqs/autoswing_not_working/kiq85zr/)！

### 感谢 [cheahjs](https://gist.github.com/cheahjs/300239464dd84fe6902893b6b9250fd0) 的非常有用的脚本，帮助我完成了这个修复！

### 感谢任何测试和解决bug的帮助。