---
title: 英雄联盟客户端API
date: 2021-07-29 21:20:51
updated: 2021-07-29 21:20:51
top_img: https://i.loli.net/2021/07/29/96jhgeHv3kE8U2S.jpg
cover: https://i.loli.net/2021/07/29/Ve1p79QjoPc2KrS.jpg
description: 用联盟客户端的API创建对局
tags:
- 工具
# 置顶优先级 数值越大优先级越高 #
sticky: 100001
# 【可选】文章分类 #
categories: 工具
# 【可选】文章关键字 #
keywords:
- LOL
- 英雄联盟
---


## 安装
#### python3
#### lcu-driver
```shell
pip install lcu-driver
```

## python脚本
```python
from lcu_driver import Connector

connector = Connector()


# -----------------------------------------------------------------------------
# 获得召唤师数据
# -----------------------------------------------------------------------------
async def get_summoner_data(connection):
    data = await connection.request('GET', '/lol-summoner/v1/current-summoner')
    summoner = await data.json()
    print(summoner)
    print(f"displayName:    {summoner['displayName']}")
    print(f"summonerId:     {summoner['summonerId']}")
    print(f"puuid:          {summoner['puuid']}")
    print("-")


# -----------------------------------------------------------------------------
#  lockfile 
# -----------------------------------------------------------------------------
async def get_lockfile(connection):
    import os
    path = os.path.join(connection.installation_path.encode('gbk').decode('utf-8'), 'lockfile')
    if os.path.isfile(path):
        file = open(path, 'r')
        text = file.readline().split(':')
        file.close()
        print(connection.address)
        print(f'riot    {text[3]}')
        return text[3]
    return None


# -----------------------------------------------------------------------------
# 创建训练模式 5V5 自定义房间
# gameMode -> 游戏模式。自定义模式为 'CLASSIC'， 训练模式为 'PRACTICETOOL' (仅召唤师峡谷) 其他开放的游戏模式通过查询
# mapId -> 地图ID。召唤师峡谷：11, 嚎哭深渊：12
# mutators -> 游戏模式。自定义模式为 'CLASSIC'， 训练模式为 'PRACTICETOOL' (仅召唤师峡谷)
# teamSize -> 队伍规模
# lobbyName -> 房间名称
# lobbyPassword -> 房间密码
# -----------------------------------------------------------------------------
async def create_custom_lobby(connection):
    custom = {
        'customGameLobby': {
            'configuration': {
                'gameMode': 'TUTORIAL_MODULE_1',
                'gameMutator': '',
                'gameServerRegion': '',
                'mapId': 11,
                'mutators': {'id': 1},
                'spectatorPolicy': 'AllAllowed',
                'teamSize': 5
            },
            'lobbyName': 'PRACTICETOOL',
            'lobbyPassword': ''
        },
        'isCustom': True
    }
    await connection.request('POST', '/lol-lobby/v2/lobby', data=custom)


# -----------------------------------------------------------------------------
# 添加单个机器人
# championId -> 英雄Id
# teamId  ->  100:是指蓝色方（左下角）   200:是指紫色方（右上角）
# botDifficulty -> 机器人难度，目前国服只有 EASY MEDIUM
# -----------------------------------------------------------------------------
async def add_bots_team1(connection):
    soraka = {
        'championId': 102,
        'botDifficulty': 'MEDIUM',
        'teamId': '100'
    }
    await connection.request('POST', '/lol-lobby/v1/lobby/custom/bots', data=soraka)


# -----------------------------------------------------------------------------
# 批量添加机器人
# -----------------------------------------------------------------------------
async def add_bots_team2(connection):
    # 获取自定义模式电脑玩家列表
    activedata = await connection.request('GET', '/lol-lobby/v2/lobby/custom/available-bots')
    champions = {
        bot['name']: bot['id'] for bot in await activedata.json()
    }

    print("获取可使用的召唤师 ：id--->name")
    for champion in champions:
        print(str(champions[champion]) + '--->' + champion)

    team2 = ['诺克萨斯之手', '牛头酋长', '曙光女神', '武器大师', '瓦洛兰之盾']
    for name in team2:
        bot = {'championId': champions[name],
               'botDifficulty': 'MEDIUM',
               'teamId': '200'}
        await connection.request('POST', '/lol-lobby/v1/lobby/custom/bots', data=bot)


# -----------------------------------------------------------------------------
# websocket
# -----------------------------------------------------------------------------
@connector.ready
async def connect(connection):
    await get_summoner_data(connection)
    await get_lockfile(connection)
    await create_custom_lobby(connection)
    await add_bots_team1(connection)
    await add_bots_team2(connection)


# -----------------------------------------------------------------------------
# Main
# -----------------------------------------------------------------------------
connector.start()

```

