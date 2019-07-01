+++
title = "Google CTF 2019 Quals"
subtitle = "Doomed to Repeat It."

# Add a summary to display on homepage (optional).
summary = "Doomed to Repeat It."

date = "2019-06-24T17:01:05+09:00"
publishDate = "2019-06-24T17:01:05+09:00"
expiryDate = ""

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = []

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["CTF", "Google", "2019", "golang"]
categories = ["CTF"]
keywords = ["CTF", "Google", "2019", "golang"]

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["deep-learning"]` references 
#   `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
# projects = ["internal-project"]

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder. 
[image]
  # Caption (optional)
  caption = ""

  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = ""

draft = true
+++

Google CTF 2019 Quals

# index
1. Misc  
1.1. [Doomed to Repeat It](#doomed-to-repeat-it) `#golang`, `#brute force`  

## Doomed to Repeat It
**본 Writeup은 [Reptilian Shapeshifters' Writeup](https://ctftime.org/writeup/15811)과 [Sl33perSh3ll's Writeup](https://ctftime.org/writeup/15815)을 재구성한 것입니다.**

solves: 65

> Play the classic game Memory. Feel free to download and study the source code.  
https://doomed.web.ctfcompetition.com/  
[Download Attachment](https://storage.googleapis.com/gctf-2019-attachments/7d6680177ddf33167700f021db01c260fac0b25cc05e28d3803a224046fee461)

65명이 푼 Doomed to Repeat It 문제입니다.
'클래식 게임인 메모리를 플레이하세요.
소스 코드를 편하게 다운로드해서 분석하세요.'
라면서 웹 사이트 링크와 사이트의 소스 코드를 제공해줍니다.

웹 사이트에 접속해보면 아래와 같이 나온다.  
![site](https://user-images.githubusercontent.com/19237789/60003195-266da180-96a5-11e9-9736-aae4298997d0.png)

{{< highlight diff >}}
Welcome to the classic game Memory.

Rules are simple: pick an unsolved tile, then pick the matching tile. Solve all the tiles to win!

You are limited in how many guesses you can make, and in how much time per guess. Guess well!

Play
{{< / highlight >}}

[Memory](https://en.wikipedia.org/wiki/Concentration_(game)): 짝 맞추기 게임 위키  
[Play](https://doomed.web.ctfcompetition.com/game.html): game.html

{{< figure title="https://ctftime.org/writeup/15815" src="https://i.imgur.com/2hGmv4K.gif" >}}

Play를 누르면 game.html 링크로 들어가집니다.
이곳에서 짝 맞추기 게임을 하는데
10초 안에 카드 하나씩 선택해야만 하고
카드를 최대 60번 뒤집어 볼 수 있습니다.
짝을 맞추려면 카드를 2번 뒤집어야만 합니다.
따라서, 최대 30번의 짝을 맞춰볼 수 있습니다.

8 x 7 이므로 56개이고
숫자가 0부터 27까지. 총 28 쌍으로 이루어져 있습니다.

그런데 **60번 중에 실패는 4번까지만 허용됩니다**.
시작할 때 후면이 위로 오도록 뒤집어져 있는 채로 시작하기 때문에
찾고자 하는 카드가 어디에 있는지 확인하기 위해서 한 번씩은 뒤집어봐야 하지만
60번의 시도 중에 56번은 정답을 맞히는 데에 써야 합니다.

그래서 main.go 소스 코드를 분석해보니 카드 배열의 경우의 수가 일부 중복되어 출현한다는 것을 알아냈습니다. (난수 발생에 문제가 있음)
10만 가지의 경우 중 중복이 23,438개가 있었고 이는 확률상으로 봤을 때 4~5번에 한 번꼴로 중복된다고 볼 수 있습니다.

익스플로잇 코드의 간략 설명:

1. websocket으로 4개의 카드를 확인
2. 미리 준비한 23,438개의 카드 배열에서 일치하는 것이 있는지 확인
3. 일치하면 56개의 카드 맞추고 Flag 출력

{{< highlight python "linenos=table" >}}
import asyncio
import json
import random
import sys
import websockets

guesses = dict()
matched = []
known = [-1 for i in range(56)]
done = False

async def guess(websocket, x, y):
    # msg 보내기
    msg = dict(op="guess", body=dict(x=x, y=y))
    await websocket.send(json.dumps(msg))
    # parse
    response_str = await websocket.recv()
    response = json.loads(response_str)
    board = response["board"]
    done = response["done"]
    index = y * 7 + x
    value = board[index]
    known[index] = value
    # print info
    print(f"{x}, {y}: {value}")
    # make pedigree
    if value not in guesses:
        guesses[value] = [index]
    else:
        guesses[value].append(index)
    # if Flag contain in response. than done
    if "Flag" in response_str:
        print(response["message"])
        done = True
    return value

async def solve(uri):
    # Returns 403 if origin header not set.
    origin = "https://doomed.web.ctfcompetition.com"
    async with websockets.client.connect(uri, ssl=True, origin=origin) as websocket:
        # start
        msg = dict(op="info")
        await websocket.send(json.dumps(msg))
        response = await websocket.recv()
        print(f'{response}')
        # make up dictionary of cases
        dupes = dict()
        with open("dupes.txt") as f:
            for line in f:
                dupe = json.loads(line)
                key = f"{dupe[0]}, {dupe[1]}, {dupe[2]}, {dupe[3]}"
                dupes[key] = dupe
        # Flip the first four cards.
        first = await guess(websocket, 0, 0)
        second = await guess(websocket, 1, 0)
        third = await guess(websocket, 2, 0)
        fourth = await guess(websocket, 3, 0)
        key = f"{first}, {second}, {third}, {fourth}"
        if key in dupes:
            print("FOUND DUPE!")
            for index, value in enumerate(dupes[key][4:]):
                # make pedigree
                if value not in guesses:
                    guesses[value] = [index + 4]
                else:
                    guesses[value].append(index + 4)
        else:
            print("No match. Game over.")
            return

        while(not done):
            for value in guesses.keys():
                if len(guesses[value]) == 2 and value not in matched:
                    first_index = guesses[value][0]
                    first_x = first_index % 7
                    first_y = first_index // 7
                    first = await guess(websocket, first_x, first_y)
                    second_index = guesses[value][1]
                    second_x = second_index % 7
                    second_y = second_index // 7
                    second = await guess(websocket, second_x, second_y)
                    if first == second:
                        matched.append(value)
                        print("MATCHED: ", matched)
                    else:
                        print("MISS!!" , first, second)
                        print("Dupe doesn't match. Game over.")
                        return
            not_found = [i for i, val in enumerate(known) if val == -1]
            if not_found:
                next_guess = random.choice(not_found)
            else:
                return
            x = next_guess % 7
            y = next_guess // 7
            first = await guess(websocket, x, y)
            if len(guesses[first]) == 2:
                next_guess = guesses[first][0]
                matched.append(first)
            else:
                not_found = [i for i, val in enumerate(known) if val == -1 and i != next_guess]
                next_guess = known.index(-1)
                next_guess = random.choice(not_found)
            x = next_guess % 7
            y = next_guess // 7
            second = await guess(websocket, x, y)
            if first not in matched:
                if first == second:
                    matched.append(first)
            print("MATCHED: ", matched)

uri = "wss://doomed.web.ctfcompetition.com/ws"
asyncio.get_event_loop().run_until_complete(solve(uri))
{{< / highlight >}}

{{< highlight go "linenos=table,linenostart=33" >}}
// We vigorously defend against CSRF/XSRF with strict origin checks.
// Note that without this, we would have severe vulnerabilities, because
// browsers don't enforce the same origin policy on websockets.
// Security is our #1 priority.
func checkOrigin(r *http.Request) bool {
    o := r.Header.Get("Origin")
    p := r.Header.Get("X-Forwarded-Proto")
    h := r.Host
    if o == "" || h == "" {
        log.Print("Websocket missing origin and/or host")
        return false
    }
    ou, err := url.Parse(o)
    if err != nil {
        log.Printf("Couldn't parse url: %v", err)
        return false
    }
    if p != "" && ou.Scheme != "https" {
        log.Print("Https websocket missing https origin")
        return false
    }
    if ou.Host != h {
        log.Print("Origin doesn't match host")
        return false
    }
    // TODO: Origin is a 3 tuple (scheme, host, port). Figure out how to check the
    // port.
    // TODO: We should enforce https origin on https ws even on non-App Engine.
    return true
}
{{< / highlight >}}

main.go 소스 코드를 확인해보면 CSRF나 XSRF를 방지하고자 <kbd>Origin</kbd>을 확인하고 있다고 한다.
그래서 websocket 보낼 때 Origin을 포함하지 않으면 403 Forbidden 이 뜬다.

{{< highlight go "linenos=table,linenostart=22" >}}
// OsRand gets some randomness from the OS.
func OsRand() (uint64, error) {
    // 64 ought to be enough for anybody
    var res uint64
    if err := binary.Read(rand.Reader, binary.LittleEndian, &res); err != nil {
        return 0, fmt.Errorf("couldn't read random uint64: %v", err)
    }
    // Mix in some of our own pre-generated randomness in case the OS runs low.
    // See Mining Your Ps and Qs for details.
    res *= 14496946463017271296
    return res, nil
}
{{< / highlight >}}

위 코드는 random/random.go 소스 코드이며 난수 발생기를 확인할 수 있습니다.

{{< highlight python >}}
>>> hex(14496946463017271296)
'0xc92f800000000000L'
>>> 
>>> res = 8979835648265197673
>>> res *= 140737488355328
>>> res = ctypes.c_uint64(res).value
>>> bin(140737488355328)
'0b100000000000000000000000000000000000000000000000'
>>> 140737488355328 == 2 ** 47
True
>>> bin(res)
'0b100110000110100100000000000000000000000000000000000000000000000'
>>> 2 ** (64-47)
131072
{{< / highlight >}}

res에 해당 숫자를 곱하면 2의 47승 아래가 전부 0으로 초기화 된다.
OsRand()는 uint64 자료형을 쓰지만 발생하는 난수의 경우의 수는 131,072(2**17)가지 밖에 안된다.
따라서, 13만 가지의 카드 배열을 레인보우 테이블처럼 만들어 놓고 풀면 된다.
