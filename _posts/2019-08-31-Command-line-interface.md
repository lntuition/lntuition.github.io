---
layout: post
title: "oclif로 쉽고 빠르게 CLI 만들기"
tags: ["Javascript"]
comments: true
---

oclif로 쉽고 빠르게 CLI 만들기

---

## 시작하면서
업무를 맡으면서 이미 만들어진 스크립트에 CLI를 적용할 일이 있었습니다.
실제로는 GUI를 만들어서 적용했지만 CLI를 만드는 것은 여건 귀찮은 일(파싱하고 매개변수 넘겨주고..)이더라구요.
그러던 와중 지인에게 oclif 라는 프레임워크를 소개받았습니다.
물론 업무에서는 사용하기 어려울 것 같지만 가지고 놀다보니 재미있어서 글로 남겨봅니다.

## 오픈소스 시작하기
oslif는 Node 환경을 이용하는 프레임워크로서 Javascript와 Typescript를 전부 지원합니다.
공식문서의 Introduce를 살펴보다 보면 다음과 같은 부분이 있네요.

> With oclif you can create 2 different CLI types: single and multi.  
> Single CLIs are like `ls` or `curl`. They can accept arguments and flags. ...  
> Multi CLIs are like `git` or `heroku`. They have subcommands that are themselves single CLIs. ... 

잘 살펴보면 Single type이 Multi type의 subset이 될 수 있는것처럼 보이네요.
만드려는 CLI의 구조를 보고 당연히 type을 선택하는게 맞지만, 저희는 사용해보는데에 의의가 있으니까 Multi type으로 설치해 보겠습니다.
터미널을 키고 `npx oclif multi` 를 입력하면 아래 리스트의 내용들을 포함하는 옵션 선택창이 뜹니다.

- Package manager : npm, yarn
- 사용 언어 : Typescript, Javascript
- ESlint의 적용 여부
- Mocha (Testing framework) 적용 여부
- CI : Circle CI, etc...

저희는 학습이 목적이니까 CI를 제외한 나머지를 전부 적용해서 실행해 봅시다.

## Testing
먼저 프로젝트에 Test framwork를 적용하였으니 test/commands 밑에 diff.test.js 파일을 작성해봅시다. 

``` javascript 
const { expect, test } = require('@oclif/test');

describe('diff', () => {
  const errorCommands = [
    ['diff'],
    ['diff', '06:40']
  ]

  errorCommands.forEach(command => {
    test
      .command(command)
      .catch(Error)
      .it('runs ' + command.join(' '));
  });

  test
    .stdout()
    .command(['diff', '06:40', '08:20'])
    .it('runs diff 06:40 08:20', context => {
      expect(context.stdout).to.contain('01:40');
    });

  test
    .stdout()
    .command(['diff', '09:20', '06:40'])
    .it('runs diff 09:20 06:40', context => {
      expect(context.stdout).to.contain('-02:40');
    });
});

``` 

다른 게시글과는 다르게 어떤 CLI를 만들 것인지를 서론에 밝히지 않은 이유는 바로 테스팅을 적용했기 때문입니다.
TDD 에서는 코드를 작성하기 전에 해당 코드에 맞는 테스트를 먼저 작성하기 때문입니다.
또한, 잘 만든 테스트는 보기만 해도 어떤 프로그램인지 쉽게 이해할 수 있기 때문이죠.
위의 테스트를 통해 만드려는 프로그램이 **'두개의 시간을 받아서 차이를 출력하는'** 프로그램임을 유추할 수 있습니다.
테스트를 다 작성했으니 `npm test`를 통해 test를 수행해보면 전부 실패합니다.

## Command
Test의 코드를 만족하도록 src/commands 밑에 diff.js를 작성하였습니다.
시간을 다루는 부분은 [Moment.js](https://momentjs.com/docs/) 을 이용하여 작성하였습니다.

``` javascript
const { Command } = require('@oclif/command');
const moment = require('moment');

class DiffCommand extends Command {
  async run() {
    const { args } = this.parse(DiffCommand);
    let diffMinute = moment(args.endTime, 'HH:mm').diff(moment(args.startTime, 'HH:mm'), 'minutes');

    let sign = '';
    if (diffMinute < 0) {
      diffMinute = -diffMinute;
      sign = '-';
    }
    let hour = String(parseInt(diffMinute / 60)).padStart(2, '0');
    let minute = String(diffMinute % 60).padStart(2, '0');

    this.log(sign + hour + ":" + minute);
  }
}

DiffCommand.description = `Print difference between two times`;
DiffCommand.args = [
  {
    name: 'startTime',
    required: true,
  },
  {
    name: 'endTime',
    required: true,
  },
]

module.exports = DiffCommand;
```

코드를 살펴보면 oclif의 진가를 알아 볼 수 있습니다.
만약 직접 CLI를 만든다고하면, argument를 파싱하는 것부터 해당 argument가 항상 필요로 하는것인지 선택적으로 필요한것인지 또는 flag를 이용하는 CLI 인지등을 직접 코드로 작성하여야 합니다.
하지만 oclif에서는 args, flag 와 같은 옵션을 제공함으로서 해당 문제점을 해결하였습니다.
위의 코드에서는 args의 option에 required 를 set 해주는 것만으로 해당 argument가 존재하지 않을 경우 알아서 oclif가 error를 throw 합니다.
코드를 작성하고 다시 `npm test`를 수행하면 All pass 함을 알 수 있습니다.

## 끝내면서
생각보다 너무 간단해서 놀랍지 않나요?
제가 적은 내용들의 모두가 공식문서에 매우 잘 설명되어 있던 것들입니다.
당연히 oclif는 위의 내용들 보다 훨씬 더 다양한 기능(가령 `inquirer`을 통한 prompting)들을 제공합니다.

이런 단순한 포스팅이라도 꾸준히 남겨놓는 것이 개인적으로는 무척이나 좋은 자산이 될거라고 생각합니다. 
조만간 또 포스팅 남길만한 일이 생긴게 있어서, 정리가 되면 다른 포스팅으로 찾아뵙겠습니다.

---
참고 : <https://oclif.io/docs/introduction>
