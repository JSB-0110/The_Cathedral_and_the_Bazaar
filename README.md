# The Cathedral and the Bazaar

Copyright © 2000 Eric S. Raymond

The Cathedral and the Bazaar by Eric S. Raymond

Copyright © 2020 Ajou University

Translate in Korean by Ajou University

Permission is granted to copy, distribute and/or modify this document under the terms of the Open Publication License, version 2.0.

$Date: 2020/10/08 09:02:14 $

### 문서이력 

* merge version 3.0 from http://www.catb.org/~esr/writings/cathedral-bazaar/cathedral-bazaar/index.html
* KLDP 번역 2011/02/08 버전을 병합함

### Abstract

I anatomize a successful open-source project, fetchmail, that was run as a deliberate test of the surprising theories about software engineering suggested by the history of Linux. I discuss these theories in terms of two fundamentally different development styles, the ``cathedral'' model of most of the commercial world versus the ``bazaar'' model of the Linux world. I show that these models derive from opposing assumptions about the nature of the software-debugging task. I then make a sustained argument from the Linux experience for the proposition that ``Given enough eyeballs, all bugs are shallow'', suggest productive analogies with other self-correcting systems of selfish agents, and conclude with some exploration of the implications of this insight for the future of software.

성공적인 오픈 소스 프로젝트인 fetchmail을 분석한다. 이 프로젝트는 리눅스의 역사에 의해 제시된 놀라운 소프트웨어 엔지니어링 이론을 신중하게 테스트하기 위해 실행된 것이다. 이 이론들을 두 개의 근본적으로 다른 개발 스타일의 용어들로 논할 것이다. 두가지 스타일이란 상업용 소프트웨어의 ``성당'' 모델과 리눅스 세계의 ``시장'' 모델이다. 이 모델들은 소프트웨어 디버깅 작업의 본질에 대한 서로 대립되는 가설들로부터 파생되었다는 것을 보일 것이다. 그리고 나서 리눅스의 경험으로부터 ``충분히 많은 사람이 있다면, 찾을 수 없는 버그란 없다'' 는 일관된 주장을 펴고, 이기적인 에이전트의 자가수정 시스템과의 생산적인 비유를 제시한 다음, 소프트웨어의 미래를 위해 이 통찰이 가지는 의미에 대한 탐구로 마무리짓는다.

### table of contents

- The Cathedral and the Bazaar
- The Mail Must Get Through
- The Importance of Having Users
- Release Early, Release Often
- How Many Eyeballs Tame Complexity
- When Is a Rose Not a Rose?
- Popclient becomes Fetchmail
- Fetchmail Grows Up
- A Few More Lessons from Fetchmail
- Necessary Preconditions for the Bazaar Style
- The Social Context of Open-Source Software
- On Management and the Maginot Line
- Epilog: Netscape Embraces the Bazaar
- Notes
- Bibliography
- Acknowledgements


### 차례

- 성당과 시장
- 메일은 배달되어야만 한다.
- 사용자가 있다는 것의 중요성
- 일찍, 그리고 자주 발표하라.
- 장미가 장미다우려면
- Popclient가 Fetchmail이 되다.
- Fetchmail 의 성장
- Fetchmail에서 배울 점
- 시장 스타일의 개발에 필요한 선행조건들
- 오픈 소스 소프트웨어의 사회적 문맥
- 감사의 글
- 읽어볼 만한 글들
- 후기 : 넷스케이프가 시장 스타일을 받아들이다!
- 버전과 변경 이력

## 성당과 시장

Linux is subversive. Who would have thought even five years ago (1991) that a world-class operating system could coalesce as if by magic out of part-time hacking by several thousand developers scattered all over the planet, connected only by the tenuous strands of the Internet?

리눅스는 파괴적이다. 파트타임으로 해킹을 하면서 인터넷이라는 가느다란 선만으로 연결되어 있는 전세계 수천명의 개발자들에 의해 세계적인 수준의 운영체제가, 마치 마술처럼 만들어질 수 있었으리라고 누가 5년 전에 감히 상상이나 할 수 있었을까? 

Certainly not I. By the time Linux swam onto my radar screen in early 1993, I had already been involved in Unix and open-source development for ten years. I was one of the first GNU contributors in the mid-1980s. I had released a good deal of open-source software onto the net, developing or co-developing several programs (nethack, Emacs's VC and GUD modes, xlife, and others) that are still in wide use today. I thought I knew how it was done.

나는 분명 상상하지 못했다. 1993년 초, 리눅스가 내 레이다 화면에 잡혔을 때 나는 이미 유닉스와 오픈 소스 개발을 10년 동안 해오고 있었으며 1980년대 중반에 GNU 에 공헌한 첫 번째 사람들 중 한명이었다. 나는 네트워크 상에 꽤 많은 오픈 소스 소프트웨어를 발표했고, 지금도 널리 사용되고 있는 몇몇 프로그램을 개발중이거나 공동개발하고 있었다. (네트핵, Emacs VC 와 GUD 모드, xlife, 등등) 나는 프로그램이 어떻게 개발되어야 하는지 알고 있다고 생각했다.

Linux overturned much of what I thought I knew. I had been preaching the Unix gospel of small tools, rapid prototyping and evolutionary programming for years. But I also believed there was a certain critical complexity above which a more centralized, a priori approach was required. I believed that the most important software (operating systems and really large tools like the Emacs programming editor) needed to be built like cathedrals, carefully crafted by individual wizards or small bands of mages working in splendid isolation, with no beta to be released before its time.

리눅스는 내가 알고 있다고 생각한 많은 부분을 뒤집어 버렸다. 몇 년 동안이나 나는 작은 도구, 빠른 프로토타이핑, 그리고 진화적인 프로그래밍을 여러 해 동안 유닉스의 복음으로 설교해 오고 있었다. 하지만 나는 어떤 종류의 매우 중요한 복잡성이 있어서 거기에는 더 집중되고 선험적인 접근방법이 필요하다고 믿고 있었다. 가장 중요한 소프트웨어 (운영체제나 Emacs 같이 대단히 커다란 도구들)는 성당을 건축하듯이, 즉 찬란한 고독 속에서 일하는 몇 명의 도사 프로그래머나 작은 그룹의 뛰어난 프로그래머들에 의해 조심스럽게 만들어지고 때가 되기 전에 발표되는 베타버전도 없어야 한다고 생각했던 것이다.

Linus Torvalds's style of development—release early and often, delegate everything you can, be open to the point of promiscuity—came as a surprise. No quiet, reverent cathedral-building here—rather, the Linux community seemed to resemble a great babbling bazaar of differing agendas and approaches (aptly symbolized by the Linux archive sites, who'd take submissions from anyone) out of which a coherent and stable system could seemingly emerge only by a succession of miracles.

리누스 토발즈의 개발 스타일은 - 일찍, 그리고 자주 발표하며 다른 사람들에게 위임할 수 있는 것은 모두 위임하고, 뒤범벅이 된 부분까지 공개하는 그런 스타일 - 나에게 놀라움으로 다가왔다. 고요하고 신성한 성당의 건축방식은 여기에서 찾아볼 수 없었다. 대신, 리눅스 공동체는 서로 다른 의견과 접근방법이 난무하는 매우 소란스러운 시장같았다. (리눅스 아카이브 사이트가 이것을 적절히 상징하고 있다. 이곳에는 누구나 파일을 올릴 수 있다) 이런 시장바닥에서 조리있고 안정적인 시스템이 나온다는 것은 거듭되는 기적에 의해서만 가능한 것처럼 보였다.

The fact that this bazaar style seemed to work, and work well, came as a distinct shock. As I learned my way around, I worked hard not just at individual projects, but also at trying to understand why the Linux world not only didn't fly apart in confusion but seemed to go from strength to strength at a speed barely imaginable to cathedral-builders.

시장 스타일이 매우 효과적이라는 사실은 분명 충격이었다. 리눅스 공동체에 익숙해져 가면서 나는 개개의 프로젝트에 열심이었을 뿐만 아니라 왜 리눅스 세계가 공중분해 되지도 않고 성당건축가들이 상상하기도 힘든 속도로 계속해서 강해지는지 이해하려고 애썼다.

By mid-1996 I thought I was beginning to understand. Chance handed me a perfect way to test my theory, in the form of an open-source project that I could consciously try to run in the bazaar style. So I did—and it was a significant success.

1996년 중반에야 이해가 되기 시작했다. 내 이론을 시험해 볼 수 있는 완벽한 기회가 오픈 소스 프로젝트의 형태로 찾아왔다. 여기에서 나는 의식적으로 시장 스타일을 시도해 볼 수 있었고, 큰 성공을 거두었다.

This is the story of that project. I'll use it to propose some aphorisms about effective open-source development. Not all of these are things I first learned in the Linux world, but we'll see how the Linux world gives them particular point. If I'm correct, they'll help you understand exactly what it is that makes the Linux community such a fountain of good software—and, perhaps, they will help you become more productive yourself.

이 글의 나머지 부분에서는 그 프로젝트에 대해 이야기하고 효과적인 오픈 소스 개발에 대한 격언들을 제시할 것이다. 내가 이 모든 것을 리눅스 세계에서 처음 배운 것은 아니지만 리눅스 세계는 이 격언들이 특별한 의미를 가질 수 있게 해주었다. 만일 내가 옳다면, 독자들은 이 격언들로부터 리눅스 공동체가 훌륭한 소프트웨어를 만들어내는 원천이 될 수 있었던 이유를 이해할 수 있을 것이며, 독자들 자신도 더 생산적으로 되는 데 도움을 받을 수 있을 것이다.

## 메일은 배달되어야만 한다.

Since 1993 I'd been running the technical side of a small free-access Internet service provider called Chester County InterLink (CCIL) in West Chester, Pennsylvania. I co-founded CCIL and wrote our unique multiuser bulletin-board software—you can check it out by telnetting to locke.ccil.org. Today it supports almost three thousand users on thirty lines. The job allowed me 24-hour-a-day access to the net through CCIL's 56K line—in fact, the job practically demanded it!

1993년에 나는 펜실베니아 주, 서 체스터(West Chester) 시의 자그마한 무료 ISP인 체스터 카운티 인터링크 (Chester County InterLink : CCIL) 에서 기술적인 측면을 담당하고 있었다. (나는 CCIL 의 공동설립자였으며 우리만의 멀티유저 게시판 소프트웨어를 작성했다 - locke.ccil.org에 telnet 으로 접속하면 볼 수 있으며 지금은 19회선으로 3000 여명의 사용자를 지원한다) 이 일 덕분에 나는 하루 24시간 내내 CCIL의 56K 회선을 통해 네트워크에 접속해 있을 수 있었다 -- 사실, 그렇게 해야만 하는 상황이었다.

I had gotten quite used to instant Internet email. I found having to periodically telnet over to locke to check my mail annoying. What I wanted was for my mail to be delivered on snark (my home system) so that I would be notified when it arrived and could handle it using all my local tools.

그래서 나는 바로바로 배달되는 인터넷 이메일에 매우 익숙해져 있었는데 몇가지 복잡한 이유들로 인해 내 집의 컴퓨터 (snark.thyrsus.com) 과 CCIL 사이에 SLIP 연결을 하기가 꽤 힘들었다. 마침내 성공하고 나자, 주기적으로 locke 에 접속해 메일이 왔는지 체크해 보는 것이 매우 귀찮은 일이라는 것을 알게 되었다. 내가 원하는 것은 내 메일이 snark 로 배달되어 도착하는 즉시 내가 그것을 알 수 있고, 내 컴퓨터의 도구들을 이용해 메일을 다룰 수 있게 되는 것이었다.

The Internet's native mail forwarding protocol, SMTP (Simple Mail Transfer Protocol), wouldn't suit, because it works best when machines are connected full-time, while my personal machine isn't always on the Internet, and doesn't have a static IP address. What I needed was a program that would reach out over my intermittent dialup connection and pull across my mail to be delivered locally. I knew such things existed, and that most of them used a simple application protocol called POP (Post Office Protocol). POP is now widely supported by most common mail clients, but at the time, it wasn't built in to the mail reader I was using.

sendmail을 이용해 단순히 포워드시키는 것은 소용이 없었다. 내 개인 컴퓨터가 항상 네트워크에 연결되어 있는 것도 아니고 고정적인 IP 어드레스를 가지고 있지도 않았다. SLIP 연결이 되면 내 메일을 가져와 내 컴퓨터 안에서 배달해주는 프로그램이 필요했다. 그런 프로그램이 몇 개 있었고, 대부분은 프로토콜로 POP(Post Office Protocol)을 사용했다. 물론, locke 의 BSD/OS 운영체제에는 POP3 서버가 포함되어 있었다.

I needed a POP3 client. So I went out on the Internet and found one. Actually, I found three or four. I used one of them for a while, but it was missing what seemed an obvious feature, the ability to hack the addresses on fetched mail so replies would work properly.

하지만 내게 필요한 것은 POP3 클라이언트였다. 그래서 네트워크를 뒤져 하나를 찾아냈다. 사실 서너개를 찾아내긴 했다. 잠시동안은 pop-perl을 사용했지만 기본적인 기능이 빠져 있었다. 가져온 메일에서 발신인의 주소를 제대로 처리하지 못해 답장을 보낼 수가 없었던 것이다.

The problem was this: suppose someone named `joe' on locke sent me mail. If I fetched the mail to snark and then tried to reply to it, my mailer would cheerfully try to ship it to a nonexistent `joe' on snark. Hand-editing reply addresses to tack on <@ccil.org> quickly got to be a serious pain.
문제는 이런 것이었다. locke 의 사용자 중에 `joe' 라는 사람이 나에게 메일을 보냈다고 해보자. snark 로 메일을 가져와서 그 메일에 답장을 하려고 하면 메일 프로그램은 snark 에는 있지도 않은 `joe' 에게 답장을 보내려고 시도한다. 그래서 손으로 `@ccil.org'를 답장 받는 사람의 주소 뒤에 붙여주어야 했는데, 이것은 곧 매우 피곤한 일이 되어버렸다.
This was clearly something the computer ought to be doing for me. But none of the existing POP clients knew how! And this brings us to the first lesson:
이런 일은 분명히 컴퓨터가 해주어야 하는 일이었다. 하지만 이미 있는 POP 클라이언트들 중에서는 어느것도 이 일을 해주지 못했다. 여기에서 첫 번째 교훈을 얻을 수 있다.

1. Every good work of software starts by scratching a developer's personal itch.

1. 모든 좋은 소프트웨어는 개발자 개인의 가려운 곳을 긁는 것으로부터 시작된다. (Every good work of software starts by scratching a developer's personal itch)

Perhaps this should have been obvious (it's long been proverbial that ``Necessity is the mother of invention'') but too often software developers spend their days grinding away for pay at programs they neither need nor love. But not in the Linux world—which may explain why the average quality of software originated in the Linux community is so high.

명확해 보이는 교훈이긴 하지만 (``필요는 발명의 어머니'' 라는 오래된 속담이 있지 않은가) 소프트웨어 개발자들은 너무나 자주, 단지 돈 때문에 그들이 필요로 하지도 않고 좋아하지도 않는 프로그램을 만들어 내는데 시간을 쓰고 있다. 하지만 리눅스 세계에서는 그렇지 않다 - 아마도 이것이 왜 리눅스 공동체에서 만들어진 소프트웨어들의 평균적인 품질이 그렇게나 좋은지를 설명해줄 것이다.

So, did I immediately launch into a furious whirl of coding up a brand-new POP3 client to compete with the existing ones? Not on your life! I looked carefully at the POP utilities I had in hand, asking myself ``Which one is closest to what I want?'' Because:

그래서 내가 이미 있는 POP3 클라이언트들과 경쟁하는 새로운 프로그램을 곧바로 코딩하기 시작했을까? 천만에. 나는 이미 가지고 있는 POP 유틸리티들을 조심스럽게 살피면서 스스로에게 물었다. ``내가 원하는 것과 가장 가까운 프로그램이 어느 것일까?'' 그 이유는

2. Good programmers know what to write. Great ones know what to rewrite (and reuse).

2. 좋은 프로그래머는 어떤 프로그램을 만들어야 할 지 안다. 위대한 프로그래머는 어떤 프로그램을 다시 만들어야 할 지 (그리고 재사용해야 할 지) 안다.

While I don't claim to be a great programmer, I try to imitate one. An important trait of the great ones is constructive laziness. They know that you get an A not for effort but for results, and that it's almost always easier to start from a good partial solution than from nothing at all.

내가 위대한 프로그래머라는 말은 아니지만 흉내내려고는 했다. 위대한 프로그래머의 중요한 특징 중 하나는 건설적인 게으름이다. 그들은 들인 노력으로가 아니라 결과로 평가받는다는 것을 알고 있으며 완전한 무에서 시작하는 것보다는 부분적으로나마 좋은 해결책에서 시작하는 것이 거의 항상 더 쉽다는 것을 알고 있다.

Linus Torvalds, for example, didn't actually try to write Linux from scratch. Instead, he started by reusing code and ideas from Minix, a tiny Unix-like operating system for PC clones. Eventually all the Minix code went away or was completely rewritten—but while it was there, it provided scaffolding for the infant that would eventually become Linux.

리누스 토발즈 를 예로 들자면 그는 맨바닥에서 Linux를 만들어 내려고 하지 않았다. 대신 그는 386 기계를 위한 Unix 비슷한 소형 OS, Minix 의 코드와 아이디어를 재사용하는 것으로부터 시작했다. 결국 모든 Minix 코드는 사라지거나 새로 쓰여졌다 -- 하지만 Minix 의 코드가 남아있을 동안 그 코드는 나중에 Linux 가 될 어린 아기의 발판 역할을 했다.

In the same spirit, I went looking for an existing POP utility that was reasonably well coded, to use as a development base.

똑같은 생각으로 나는 이미 있는 POP 유틸리티 중 코딩이 잘 되어있는 것을 찾아 개발의 기초로 사용하려 했다.

The source-sharing tradition of the Unix world has always been friendly to code reuse (this is why the GNU project chose Unix as a base OS, in spite of serious reservations about the OS itself). The Linux world has taken this tradition nearly to its technological limit; it has terabytes of open sources generally available. So spending time looking for some else's almost-good-enough is more likely to give you good results in the Linux world than anywhere else.

Unix 세계의 소스를 공유하는 전통은 언제나 코드 재사용에 대해 호의적이었다. (GNU 프로젝트가 Unix 자체에 대한 심각한 의혹에도 불구하고 Unix 를 기본 OS 로 선택한 것도 바로 이런 이유에서였다) 리눅스 세계는 거의 기술적인 한계에 다다를 때까지 이 전통을 받아들였다. 일반적으로 찾아볼 수 있는 오픈된 소스가 수 테라바이트에 달하는 것이다. 그래서 리눅스 세계에서는 다른 어느 곳에서보다 누군가의 거의 완성된 소스를 찾아보는데 시간을 들이는 것이 좋은 결과를 가져다 줄 가능성이 높다.

And it did for me. With those I'd found earlier, my second search made up a total of nine candidates—fetchpop, PopTart, get-mail, gwpop, pimp, pop-perl, popc, popmail and upop. The one I first settled on was `fetchpop' by Seung-Hong Oh. I put my header-rewrite feature in it, and made various other improvements which the author accepted into his 1.9 release.

나에게도 역시 그랬다. 예전에 찾아놓은 것에다가 두 번째 검색결과를 더하니 모두 아홉 개의 후보가 생겼다. fetchpop, PopTart, get-amil, gwpop, pimp, pop-perl, popc, popmail, 그리고 upop 이었다. 내가 제일 먼저 정착한 프로그램은 오승홍 씨의 fetchpop 이었다. 헤더 재작성 기능과 더불어 몇몇 개선사항을 추가했고, 저자가 릴리즈 1.9 에 그것을 수용했다.

A few weeks later, though, I stumbled across the code for popclient by Carl Harris, and found I had a problem. Though fetchpop had some good original ideas in it (such as its background-daemon mode), it could only handle POP3 and was rather amateurishly coded (Seung-Hong was at that time a bright but inexperienced programmer, and both traits showed). Carl's code was better, quite professional and solid, but his program lacked several important and rather tricky-to-implement fetchpop features (including those I'd coded myself).

몇 주 후에 나는 Carl Harris 가 만든 popclient 의 코드를 들여다 보다가 문제점을 발견했다. fetchpop 에는 훌륭한 독창적인 아이디어가 들어 있었지만 (daemon 모드 같은 것) POP3 만을 처리할 수 있었고, 아마추어 티가 나는 코딩이었다. (오승홍 씨는 똑똑하기는 하지만 경험이 부족한 프로그래머였으며 그 두 가지 특징 모두를 코딩에서 볼 수 있었다) Carl 의 코드는 전문가가 만든 탄탄하면서 더 나은 코드였으나 몇가지 중요하면서도 구현하기 위해서는 약간의 잔머리가 필요한 fetchpop 의 기능들이 (내가 추가한 기능들을 포함해서) 빠져 있었다.

Stay or switch? If I switched, I'd be throwing away the coding I'd already done in exchange for a better development base.

머물러 있을 것인가, 옮겨갈 것인가? 옮겨간다면 더 나은 개발기반을 위해 이미 해놓은 코딩을 포기해야만 했다.

A practical motive to switch was the presence of multiple-protocol support. POP3 is the most commonly used of the post-office server protocols, but not the only one. Fetchpop and the other competition didn't do POP2, RPOP, or APOP, and I was already having vague thoughts of perhaps adding IMAP (Internet Message Access Protocol, the most recently designed and most powerful post-office protocol) just for fun.

옮겨가는데 실질적인 동기가 되었던 것은 다중 프로토콜 지원 여부였다. POP3 가 우체국 서버 프로토콜 중에서 가장 널리 쓰이는 것이긴 했지만 유일한 프로토콜은 아니었다. fetchpop 을 비롯하여 다른 경쟁자들은 POP2, RPOP, 또는 APOP 를 지원하지 않았고, 나는 당시에 재미삼아서 IMAP(Internet Message Access Protocol, 가장 최근에 고안되었으며 가장 강력한 우체국 프로토콜) 을 지원해 볼까 하는 생각을 가지고 있었다.

But I had a more theoretical reason to think switching might be as good an idea as well, something I learned long before Linux.

하지만 옮겨가는 것이 좋은 생각이라는 좀 더 이론적인 이유도 가지고 있었다. 리눅스를 알기 오래전에 배운 교훈이었다.

3. ``Plan to throw one away; you will, anyhow.'' (Fred Brooks, The Mythical Man-Month, Chapter 11)

3. ``가지고 있는 것을 버릴 계획을 세우라 ; 언젠가는 버리게 될 것이다 (Plan to throw one away; youu will anyhow)'' (Fred Brooks, ``The Mythical Man-Month'', Chapter 11)

Or, to put it another way, you often don't really understand the problem until after the first time you implement a solution. The second time, maybe you know enough to do it right. So if you want to get it right, be ready to start over at least once [JB].

다른 말로 하자면, 첫 번째 해결책을 구현할 때까지도 진짜 문제가 무엇인지 이해하지 못하는 경우가 종종 있다는 것이다. 두 번째가 되어서야 어떻게 하는 것이 옳은 것인지 충분히 알게 될 수 있다. 따라서 만일 올바른 방법을 찾고 싶다면 최소한 한 번은 처음부터 다시 시작할 준비를 해 두어야 한다. 

Well (I told myself) the changes to fetchpop had been my first try. So I switched.

그래, fetchpop을 고친 것은 내 첫 번째 시도였어, 하고 스스로에게 말하고 나서 나는 popclient 로 옮겨갔다.

After I sent my first set of popclient patches to Carl Harris on 25 June 1996, I found out that he had basically lost interest in popclient some time before. The code was a bit dusty, with minor bugs hanging out. I had many changes to make, and we quickly agreed that the logical thing for me to do was take over the program.

1996년 6월 25일에 Carl Harris 에게 내 첫 번째 popclient 패치를 보낸 후, 나는 그가 popclient 에 대한 흥미를 이미 잃었다는 것을 알게 되었다. 코딩이 좀 지저분했고, 자잘한 버그들이 널려있었다. 내가 수정해야 할 것이 많았고, Carl 과 나는 곧 내가 프로그램을 넘겨받는 것이 합리적이라는 데에 동의하게 되었다. 

Without my actually noticing, the project had escalated. No longer was I just contemplating minor patches to an existing POP client. I took on maintaining an entire one, and there were ideas bubbling in my head that I knew would probably lead to major changes.

내가 알아차리지 못하는 새에 프로젝트가 차츰 궤도에 오르기 시작했다. 나는 이미 존재하고 있는 POP 클라이언트의 마이너 패치를 생각하는 것이 아니었다. 클라이언트 하나를 통채로 관리하고 있었으며 내 머리에서는 커다란 변화가 될 아이디어들이 솟아나고 있었다.

In a software culture that encourages code-sharing, this is a natural way for a project to evolve. I was acting out this principle:

코드 공유를 장려하는 소프트웨어 문화에서는 이런 방식으로 프로젝트가 진화하기 마련이다. 이렇게 말할 수 있다.

4. If you have the right attitude, interesting problems will find you.

4. 적절한 태도를 가지고 있으면 흥미로운 문제가 당신을 찾아갈 것이다. 

But Carl Harris's attitude was even more important. He understood that

하지만 Carl Harris 의 태도가 훨씬 더 중요했다. 그는 이것을 이해하고 있었다.

5. When you lose interest in a program, your last duty to it is to hand it off to a competent successor.

5. 프로그램에 흥미를 잃었다면 프로그램에 대한 당신의 마지막 의무는 능력있는 후임자에게 프로그램을 넘겨주는 것이다. (When you lose interest in a program, your last duty to it is to hand it off to a competent successor)

Without ever having to discuss it, Carl and I knew we had a common goal of having the best solution out there. The only question for either of us was whether I could establish that I was a safe pair of hands. Once I did that, he acted with grace and dispatch. I hope I will do as well when it comes my turn.

토론할 필요도 없이 Carl 과 나는 우리가 가장 좋은 해결책을 찾고 있다는 것을 알고 있었다. 우리에게 남아있는 한가지 문제는 내가 적임자라는 것을 입증할 수 있느냐 하는 것이었다. 내가 그것을 증명하자 그는 기꺼이, 그리고 신속하게 행동했다. 내가 그렇게 행동할 차례가 되었을 때 나도 그만큼 잘 할 수 있기를 바란다.

## The Importance of Having Users

## 사용자가 있다는 것의 중요성

And so I inherited popclient. Just as importantly, I inherited popclient's user base. Users are wonderful things to have, and not just because they demonstrate that you're serving a need, that you've done something right. Properly cultivated, they can become co-developers.

그래서 내가 popclient를 넘겨 받았다. 내가 popclient의 사용자들을 넘겨받았다는 것도 그에 못지않게 중요하다. 사용자들이 있다는 것은 매우 좋은 일이다. 당신이 누군가의 필요를 충족시켜주고 있으며 일을 잘 해나가고 있다는 것을 보여주기 때문만은 아니다. 적절하게 유도해 준다면 사용자들은 공동개발자가 될 수도 있다.

Another strength of the Unix tradition, one that Linux pushes to a happy extreme, is that a lot of users are hackers too. Because source code is available, they can be effective hackers. This can be tremendously useful for shortening debugging time. Given a bit of encouragement, your users will diagnose problems, suggest fixes, and help improve the code far more quickly than you could unaided.

유닉스의 전통이 가지고 있는 또하나의 강점, 즉 많은 수의 사용자들이 동시에 해커이기도 하다는 것을 리눅스는 좋은 의미로서의 극단까지 밀어붙였다. 소스코드가 공개되어 있기 때문에 그들은 효과적인 해커가 될 수 있다. 이것은 디버깅 시간을 줄이는 데 엄청난 도움이 되었다. 조금만 격려해주면 사용자들은 문제를 분석하고 해결책을 제시하며, 도움 없이 혼자 일할 때보다 훨씬 빨리 코드를 개선시키도록 해준다.

6. Treating your users as co-developers is your least-hassle route to rapid code improvement and effective debugging.

6. 사용자들을 공동개발자로 생각하면 코드가 다른 어떤 방법보다도 빠른 속도로 개선되며 효율적으로 디버깅할 수 있다. (Treating your users as co-developers is your least-hassle route to rapid code improvement and effective debugging)

The power of this effect is easy to underestimate. In fact, pretty well all of us in the open-source world drastically underestimated how well it would scale up with number of users and against system complexity, until Linus Torvalds showed us differently.

이 효과의 위력은 과소평가되기 쉽다. 사실, 오픈 소스 세계의 우리들조차 시스템의 복잡도에 대항하여 많은 수의 사용자가 얼마나 힘이 되는지를 리누스 토발즈가 보여주기 전까지는 과소평가하고 있었다.

In fact, I think Linus's cleverest and most consequential hack was not the construction of the Linux kernel itself, but rather his invention of the Linux development model. When I expressed this opinion in his presence once, he smiled and quietly repeated something he has often said: ``I'm basically a very lazy person who likes to get credit for things other people actually do.'' Lazy like a fox. Or, as Robert Heinlein famously wrote of one of his characters, too lazy to fail.

실제로 나는 리누스의 가장 영리하고 가장 중요한 해킹은 리눅스 커널을 만들었다는 점이 아니라 리눅스 개발모델을 만들었다는 점이라고 생각한다. 리누스에게 이 의견을 말해 주었더니 그는 씨익 웃고서 조용히 여러번 하던 말을 되풀이했다. ``난 기본적으로 매우 게으른 사람이라서 실제로는 다른 사람들이 해놓은 일을 가지고 공로라고 인정받곤 해요.'' 여우처럼 게으르군. 로버트 하인라인이라면 `실패하기에는 너무 게으르다' 고 말했을 것이다.

In retrospect, one precedent for the methods and success of Linux can be seen in the development of the GNU Emacs Lisp library and Lisp code archives. In contrast to the cathedral-building style of the Emacs C core and most other GNU tools, the evolution of the Lisp code pool was fluid and very user-driven. Ideas and prototype modes were often rewritten three or four times before reaching a stable final form. And loosely-coupled collaborations enabled by the Internet, a la Linux, were frequent.

되돌아 보면, 리눅스의 성공과 방법론은 GNU Emacs Lisp 라이브러리와 Lisp 코드 아카이브에서 그 선례를 찾아볼 수 있다. Emacs C 코어와 다른 대부분의 FSF 도구들의 성당건축 스타일과는 대조적으로 Lisp 코드 풀의 진화는 유동적이었고, 사용자가 주도한 것이었다. 아이디어와 프로토타입 모드들은 안정적인 최종형태를 갖추기까지 종종 서너번씩 다시 쓰여졌다. 느슨하게 묶인 공동작업이 인터넷으로 인해 가능해졌고, 리눅스에서처럼 매우 자주 일어나는 일이 되었다.

Indeed, my own most successful single hack previous to fetchmail was probably Emacs VC (version control) mode, a Linux-like collaboration by email with three other people, only one of whom (Richard Stallman, the author of Emacs and founder of the Free Software Foundation) I have met to this day. It was a front-end for SCCS, RCS and later CVS from within Emacs that offered ``one-touch'' version control operations. It evolved from a tiny, crude sccs.el mode somebody else had written. And the development of VC succeeded because, unlike Emacs itself, Emacs Lisp code could go through release/test/improve generations very quickly.

사실 fetchmail 이전에 내 자신의 가장 성공적인 해킹은 아마 Emacs VC 모드였을 것이다. 세 명의 사람들과 email을 통해 리눅스와 비슷한 협동작업을 했고, 지금까지 그 셋 중의 한 명 (리차드 스톨만:Richard Stallman. Emacs 의 저자이면서 FSF 의 설립자) 만을 만나보았다. VC 모드는 SCCS, RCV 와 CVS 를 위한 Emacs 내의 프론트엔드였고, ``원터치'' 버전컨트롤 기능을 제공했다. 이것은 누군가 만들어 놓은 작고 조악한 sccs.el 모드로부터 진화한 것이었다. VC 의 개발은 Emacs 와는 다르게 Emacs Lisp 코드가 발표/테스트/개선의 주기를 매우 빨리 반복할 수 있었기 때문에 성공했다.

The Emacs story is not unique. There have been other software products with a two-level architecture and a two-tier user community that combined a cathedral-mode core and a bazaar-mode toolbox. One such is MATLAB, a commercial data-analysis and visualization tool. Users of MATLAB and other products with a similar structure invariably report that the action, the ferment, the innovation mostly takes place in the open part of the tool where a large and varied community can tinker with it.

코드를 법적으로 GPL에 묶어두려는 FSF의 정책은 한가지 예기치 못한 부작용을 가져왔는데, 그것은 FSF가 시장모드를 사용하는 것이 절차적으로 까다로워졌다는 것이다. 그들은 GPL 코드를 저작권법 하에서의 도전으로부터 면역시키기 위해 20줄 이상의 개인적인 공헌에 대해서는 저작권을 주어야 한다고 믿기 때문이다. BSD와 MIT의 X 콘소시엄 라이센스를 사용하여 저작권을 얻는 사람들에게는 이런 문제가 없다. 그들은 누군가가 도전할 동기를 가질만한 권리를 가지려 하지 않는다.

## Release Early, Release Often

## 일찍, 그리고 자주 발표하라.

Early and frequent releases are a critical part of the Linux development model. Most developers (including me) used to believe this was bad policy for larger than trivial projects, because early versions are almost by definition buggy versions and you don't want to wear out the patience of your users.

일찍, 그리고 자주 발표하는 것은 리눅스 개발 모델의 중요한 부분이다. 대부분의 개발자들은 (필자를 포함하여) 아주 사소한 프로젝트가 아니라면 이런 정책은 나쁜 것이라고 생각했다. 초기버전들은 예외없이 버그가 많고, 개발자라면 사용자들의 인내심을 시험하고 싶지는 않기 때문이다.

This belief reinforced the general commitment to a cathedral-building style of development. If the overriding objective was for users to see as few bugs as possible, why then you'd only release a version every six months (or less often), and work like a dog on debugging between releases. The Emacs C core was developed this way. The Lisp library, in effect, was not—because there were active Lisp archives outside the FSF's control, where you could go to find new and development code versions independently of Emacs's release cycle [QR].

이런 믿음이 성당건축 스타일의 개발을 더 선호하게 만들었다. 만일 가장 중요한 목표가 사용자들로 하여간 가능한 한 적은 버그를 발견하게 만드는 것이라면 6 개월에 한 번씩 (혹은 그보다 더 늦게) 발표하면서 그동안 죽어라고 일하는 편이 나을 것이다. Emacs C 코어는 이런 식으로 개발되었다. Lisp 라이브러리는 그렇지 않았다. Emacs 의 발표주기와 관계없이 언제든 새로운 개발 코드 버전을 찾을 수 있으며, FSF 의 통제권 밖에 있는 Lisp 라이브러리들이 있었기 때문이다.

The most important of these, the Ohio State Emacs Lisp archive, anticipated the spirit and many of the features of today's big Linux archives. But few of us really thought very hard about what we were doing, or about what the very existence of that archive suggested about problems in the FSF's cathedral-building development model. I made one serious attempt around 1992 to get a lot of the Ohio code formally merged into the official Emacs Lisp library. I ran into political trouble and was largely unsuccessful.

이들 중 가장 중요한 아카이브는 오늘날 대형 리눅스 아카이브들의 정신과 많은 기능들을 이미 가지고 있었던 오하이오 주의 elisp 아카이브였다. 하지만 우리가 하고 있는 일에 대해, FSF 의 성당건축 개발모델의 문제점들에 대해 그 아카이브의 존재가 무엇을 제시하는지에 대해 우리들 중 소수만이 진지하게 생각하고 있었다. 나는 1992년에 오하이오 코드를 공식적인 Emacs Lisp 라이브러리에 정식으로 병합시키려는 시도를 했으나 정치적인 문제에 부딪쳤고, 큰 실패를 겪었다.

But by a year later, as Linux became widely visible, it was clear that something different and much healthier was going on there. Linus's open development policy was the very opposite of cathedral-building. Linux's Internet archives were burgeoning, multiple distributions were being floated. And all of this was driven by an unheard-of frequency of core system releases.

1년 후에, 리눅스가 널리 알려지기 시작했고, 무언가 다르면서도 훨씬 바람직한 일이 일어나고 있다는 것이 확실해 보였다. 리누스의 열린 개발정책은 성당건축과 완전히 반대되는 것이었다. 선사이트와 tsx-11 아카이브가 싹트고 있었고, 다중배포방식이 퍼지기 시작했다. 그리고 이 모든 것이 이전의 어느 소프트웨어보다 자주 릴리즈되는 코어시스템에 의해 주도되고 있었다.

Linus was treating his users as co-developers in the most effective possible way:

리누스는 가장 효과적인 방식으로 사용자들을 공동개발자라고 여겼던 것이다.

7. Release early. Release often. And listen to your customers.

7. 일찍 발표하고 자주 발표하라. 그리고 사용자들의 소리에 귀를 기울이라. (Release early. Release often. And listen to your customers)

Linus's innovation wasn't so much in doing quick-turnaround releases incorporating lots of user feedback (something like this had been Unix-world tradition for a long time), but in scaling it up to a level of intensity that matched the complexity of what he was developing. In those early times (around 1991) it wasn't unknown for him to release a new kernel more than once a day! Because he cultivated his base of co-developers and leveraged the Internet for collaboration harder than anyone else, this worked.

리누스의 혁신은 그가 이렇게 했다는 점 보다는 (그 비슷한 것이 오랫동안 유닉스 세계의 전통이었다) 그가 개발하고 있던 리눅스 커널의 복잡성에 비견될만한 수준으로까지 끌어올렸다는 데 있다. 초기에 (1991년 경에) 그는 하루에 한 번 이상 새로운 커널을 발표하기까지 했다. 리누스가 공동개발자들이라는 자신의 기반을 잘 만들었고, 인터넷이라는 지렛대를 이용하여 누구보다도 열심히 협동작업에 몰두했기 때문에 이런 방식은 성공했다.

But **how** did it work? And was it something I could duplicate, or did it rely on some unique genius of Linus Torvalds?

하지만 어떤 과정을 거쳐 성공할 수 있었을까? 내가 재현할 수 있는 것일까, 아니면 리누스 토발즈만의 천재성이 필요한 것일까?

I didn't think so. Granted, Linus is a damn fine hacker. How many of us could engineer an entire production-quality operating system kernel from scratch? But Linux didn't represent any awesome conceptual leap forward. Linus is not (or at least, not yet) an innovative genius of design in the way that, say, Richard Stallman or James Gosling (of NeWS and Java) are. Rather, Linus seems to me to be a genius of engineering and implementation, with a sixth sense for avoiding bugs and development dead-ends and a true knack for finding the minimum-effort path from point A to point B. Indeed, the whole design of Linux breathes this quality and mirrors Linus's essentially conservative and simplifying design approach.

그렇게 생각되지는 않았다. 리누스가 매우 뛰어난 해커라는 점은 인정한다. (우리중에 상업용 제품 못지 않은 운영체제의 커널을 만들어낼 수 있는 사람이 몇이나 될까?) 하지만 리눅스는 놀랄만한 개념적 전진을 이루어내지는 않았다. 리누스는 리차드 스톨먼이나 제임스 고슬링 (NeWS 와 자바를 만든) 과 같은 혁신적인 설계를 이루어내는 천재는 (적어도 지금까지는) 아니었다. 대신 리누스는 공학의 천재인 것으로 보인다. 버그와 개발의 막다른 골목을 피하는 육감, 그리고 A 점에서 B 점까지 가는데 최소노력 경로를 찾아내는 요령을 갖추고 있었다. 실제로 리눅스의 전반적인 설계는 이런 특성을 바탕으로 하고 있으며 리누스의 본질적으로 보수적이고 단순한 설계 방식을 반영하고 있다.

So, if rapid releases and leveraging the Internet medium to the hilt were not accidents but integral parts of Linus's engineering-genius insight into the minimum-effort path, what was he maximizing? What was he cranking out of the machinery?

따라서 빠른 릴리즈와 인터넷을 매체로 사용하는 것이 우연히 이루어진 것이 아니라 리누스의 공학적 천재성에 기인한 최소노력 경로에 대한 통찰력의 통합적인 부분이었다면 그가 최대화하고 있는 것은 무엇이었을까? 기계에서 무엇을 뽑아내었던 것일까?

Put that way, the question answers itself. Linus was keeping his hacker/users constantly stimulated and rewarded—stimulated by the prospect of having an ego-satisfying piece of the action, rewarded by the sight of constant (even daily) improvement in their work.

해답은 질문 안에 있다. 리누스는 그의 해커/사용자들에게 지속적인 자극과 보답을 제공했다 -- 리눅스 개발에 참여함으로써 자기만족을 얻으리라는 전망에 자극받았고, 그들이 하는 일이 계속해서 (어떤 때는 날마다) 향상되고 있다는 것이 보답이 되었다.

Linus was directly aiming to maximize the number of person-hours thrown at debugging and development, even at the possible cost of instability in the code and user-base burnout if any serious bug proved intractable. Linus was behaving as though he believed something like this:

리누스는 만일 처리하기 곤란한 심각한 버그가 발견되면 사용자들이 떨어져 나갈 위험과 코드가 불안정해질 가능성을 무릅쓰고 디버깅과 개발에 투입되는 공수(the number of person-hours)를 최대화 하는 것에 목표를 두었다. 리누스는 다음과 같은 신념을 가지고 있는 것처럼 행동했다.

8. Given a large enough beta-tester and co-developer base, almost every problem will be characterized quickly and the fix obvious to someone.

8. 충분히 많은 베타테스터와 공동개발자가 있으면 거의 모든 문제들은 빨리 파악될 것이고 쉽게 고치는 사람이 있게 마련이다. (Given a large enough beta-tester and co-developer base, almost every problem will be characterized quickly and the fix obvious to someone)

Or, less formally, ``Given enough eyeballs, all bugs are shallow.'' I dub this: ``Linus's Law''.

덜 형식적으로 말하자면, ``보고 있는 눈이 충분히 많으면 찾지 못할 버그는 없다.'' 나는 이것을 ``리누스의 법칙'' 이라고 부른다.

My original formulation was that every problem ``will be transparent to somebody''. Linus demurred that the person who understands and fixes the problem is not necessarily or even usually the person who first characterizes it. ``Somebody finds the problem,'' he says, ``and somebody else understands it. And I'll go on record as saying that finding it is the bigger challenge.'' That correction is important; we'll see how in the next section, when we examine the practice of debugging in more detail. But the key point is that both parts of the process (finding and fixing) tend to happen rapidly.

내 원래의 공식적인 서술은 모든 문제는 ``누군가에게는 간단할 것이다'' 였다. 리누스는 문제를 이해하고 고치는 사람이 그 문제를 처음 파악한 사람과 항상 같은 것이 아니라 오히려 다른 경우가 더 많다고 이의를 제기했다. 리누스의 얘기로는, ``누군가 문제를 발견합니다. 그리고 또다른 누군가가 그 문제를 이해하지요. 문제를 발견해 내는 것이 더 중요한 일이라고 분명히 말할 수 있습니다.'' 하지만 가장 중요한 점은 사람이 충분히 많을 경우 이 두 가지가 모두 매우 빨리 일어나는 경향이 있다는 것이다.

In Linus's Law, I think, lies the core difference underlying the cathedral-builder and bazaar styles. In the cathedral-builder view of programming, bugs and development problems are tricky, insidious, deep phenomena. It takes months of scrutiny by a dedicated few to develop confidence that you've winkled them all out. Thus the long release intervals, and the inevitable disappointment when long-awaited releases are not perfect.

내 생각에는 여기에 성당 건축과 시장 스타일의 핵심적인 차이점이 있다. 프로그래밍의 성당 건축가 관점에서 보자면 버그와 개발 문제는 어렵고, 까다로우며 심오한 현상이다. 문제를 해결하려면 헌신적인 소수의 사람이 몇 달이고 정밀한 검사를 수행해야 모두 끝났다는 확신을 가질 수 있다. 따라서 발표 사이의 기간이 길어지고, 오랫동안 기다린 릴리즈가 완벽하지 않을 때는 필연적으로 실망이 따른다.

In the bazaar view, on the other hand, you assume that bugs are generally shallow phenomena—or, at least, that they turn shallow pretty quickly when exposed to a thousand eager co-developers pounding on every single new release. Accordingly you release often in order to get more corrections, and as a beneficial side effect you have less to lose if an occasional botch gets out the door.

반면, 시장의 관점에서는 버그가 보통 쉽게 해결될 수 있는 것이라고 본다 -- 최소한 새로운 릴리즈가 나올때마다 그것과 씨름하는 수천의 열정적인 공동개발자들에게 알려진다면 금방 쉽게 해결할 수 있는 문제로 바뀐다. 따라서 더 많이 교정을 받고 싶다면 자주 발표해야 하며 덤으로 서투른 부분이 드러나더라도 잃을 것이 적다는 이점이 있다.

And that's it. That's enough. If ``Linus's Law'' is false, then any system as complex as the Linux kernel, being hacked over by as many hands as the that kernel was, should at some point have collapsed under the weight of unforseen bad interactions and undiscovered ``deep'' bugs. If it's true, on the other hand, it is sufficient to explain Linux's relative lack of bugginess and its continuous uptimes spanning months or even years.

바로 이것이다. 이것으로 충분하다. ``리누스의 법칙'' 이 틀렸다면 리눅스 커널과 같이 복잡한 시스템은 어떤 것이라도 수많은 손들에 의해 해킹되면서 일찍이 볼 수 없었던 나쁜 상호작용과 발견되지 못한 ``심오한'' 버그들에 의해 어느 시점에선가 붕괴되고 말았을 것이다. 반면에, 만일 그 법칙이 옳다면 그 법칙만으로도 리눅스의 상대적으로 적은 버그를 설명할 수 있다.

Maybe it shouldn't have been such a surprise, at that. Sociologists years ago discovered that the averaged opinion of a mass of equally expert (or equally ignorant) observers is quite a bit more reliable a predictor than the opinion of a single randomly-chosen one of the observers. They called this the Delphi effect. It appears that what Linus has shown is that this applies even to debugging an operating system—that the Delphi effect can tame development complexity even at the complexity level of an OS kernel. [CV]

그리고 이 법칙이 옳다는 것에 대해서 너무 놀라지 말아야할 것이다. 수년 전, 사회학자들은 비슷하게 전문적인 (혹은 비슷하게 무지한) 관찰자들로 이루어진 대중의 평균적인 의견이 그 관찰자 중 무작위로 뽑은 한 명의 의견보다 더 신뢰할 만하다는 점을 발견했다. 사회학자들은 이것을 ``델파이 효과'' 라고 부른다. 리누스가 보여준 것은 이 효과가 운영체제를 디버깅하는 데에도 적용될 수 있다는 점이다. 델파이 효과는 OS 커널만큼 복잡한 개발까지도 다룰 수 있는 것이다.

One special feature of the Linux situation that clearly helps along the Delphi effect is the fact that the contributors for any given project are self-selected. An early respondent pointed out that contributions are received not from a random sample, but from people who are interested enough to use the software, learn about how it works, attempt to find solutions to problems they encounter, and actually produce an apparently reasonable fix. Anyone who passes all these filters is highly likely to have something useful to contribute.

Linus's Law can be rephrased as ``Debugging is parallelizable''. Although debugging requires debuggers to communicate with some coordinating developer, it doesn't require significant coordination between debuggers. Thus it doesn't fall prey to the same quadratic complexity and management costs that make adding developers problematic.

고맙게도 제프 덧키(Jeff Dutky) <dutky@wam.umd.edu> 는 리누스의 법칙을 ``디버깅은 병렬처리가 가능하다'' 는 말로 표현할 수 있음을 지적해 주었다. 제프는 디버거들이 디버깅을 하려면 의사소통을 조정해주는 개발자가 필요하지만 디버거들 사이에는 그다지 조정이 필요하지 않다고 진술한다. 따라서 개발자를 추가하는데서 생기는 기하급수적인 복잡성과 관리의 어려움이 디버깅에는 짐이 되지 않는다.

In practice, the theoretical loss of efficiency due to duplication of work by debuggers almost never seems to be an issue in the Linux world. One effect of a ``release early and often'' policy is to minimize such duplication by propagating fed-back fixes quickly [JH].

실제로 리눅스 세계에서는 디버거의 작업이 중복됨으로써 생기는 이론적인 효율 저하가 거의 문제되었던 적이 없는 것으로 보인다. ``빨리, 그리고 자주 발표하는 정책'' 의 효과 중 하나는 피드백되어 오는 수정사항을 빨리 전파함으로써 중복이 최소화된다는 것이다. 

Brooks (the author of The Mythical Man-Month) even made an off-hand observation related to this: ``The total cost of maintaining a widely used program is typically 40 percent or more of the cost of developing it. Surprisingly this cost is strongly affected by the number of users. More users find more bugs.'' [emphasis added].

브룩스(Brooks)는 제프의 진술과 관련하여 즉석에서 다음과 같은 말을 했다. ``널리 사용되는 프로그램의 유지보수에 들어가는 비용은 보통 개발시 드는 비용의 40 퍼센트나 그 이상입니다. 놀랍게도 이 비용은 사용자의 수에 큰 영향을 받습니다. 더 많은 사용자들이 더 많은 버그를 찾아냅니다.''

More users find more bugs because adding more users adds more different ways of stressing the program. This effect is amplified when the users are co-developers. Each one approaches the task of bug characterization with a slightly different perceptual set and analytical toolkit, a different angle on the problem. The ``Delphi effect'' seems to work precisely because of this variation. In the specific context of debugging, the variation also tends to reduce duplication of effort.

사용자들이 많아지면 프로그램을 시험해보는 방법이 더 늘어나기 때문에 버그를 더 많이 잡아낼 수 있다. 이 효과는 사용자들이 공동개발자들일 때 더욱 커진다. 각 사람들이 버그를 찾아낼 때 조금씩 다른 개념의 집합과 분석 도구들을 사용하여 문제의 다른 각도에서 접근하기 때문이다. ``델파이 효과'' 는 바로 이런 편차에서 비롯되는 것으로 보인다. 또한 디버깅이라는 특정한 환경에서 이 편차는 노력의 중복을 줄여주는 경향이 있다.

So adding more beta-testers may not reduce the complexity of the current ``deepest'' bug from the developer's point of view, but it increases the probability that someone's toolkit will be matched to the problem in such a way that the bug is shallow to that person.

따라서 더 많은 베타테스터를 가지는 것은 개발자의 관점에서 현재의 ``가장 심오한'' 버그의 복잡성을 줄여주지는 않을 테지만, 누군가의 도구가 문제에 딱 들어맞아 그 버그가 그 사람에게는 쉽게 잡을 수 있는 것이 될 가능성을 높여준다.

Linus coppers his bets, too. In case there are serious bugs, Linux kernel version are numbered in such a way that potential users can make a choice either to run the last version designated ``stable'' or to ride the cutting edge and risk bugs in order to get new features. This tactic is not yet systematically imitated by most Linux hackers, but perhaps it should be; the fact that either choice is available makes both more attractive. [HBS]

리누스도 물론 할 일이 있었다. 심각한 버그가 있을 경우에 대비해 리눅스 커널 버전은 잠재 사용자들이 최종적으로 ``안정된'' 버전을 사용할 수도 있고 새로운 기능을 사용하기 위해 최신의 버그가 있을 수 있는 버전을 사용할 수도 있게 번호가 붙여졌다. 이 전술은 아직까지 대부분의 리눅스 해커들이 따라하지는 않고 있지만 아마도 따라하게 될 것이다. 두 가지 선택이 가능하다는 사실이 양쪽 모두를 더 매력적으로 보이게 한다.

## How Many Eyeballs Tame Complexity

It's one thing to observe in the large that the bazaar style greatly accelerates debugging and code evolution. It's another to understand exactly how and why it does so at the micro-level of day-to-day developer and tester behavior. In this section (written three years after the original paper, using insights by developers who read it and re-examined their own behavior) we'll take a hard look at the actual mechanisms. Non-technically inclined readers can safely skip to the next section.

One key to understanding is to realize exactly why it is that the kind of bug report non–source-aware users normally turn in tends not to be very useful. Non–source-aware users tend to report only surface symptoms; they take their environment for granted, so they (a) omit critical background data, and (b) seldom include a reliable recipe for reproducing the bug.

The underlying problem here is a mismatch between the tester's and the developer's mental models of the program; the tester, on the outside looking in, and the developer on the inside looking out. In closed-source development they're both stuck in these roles, and tend to talk past each other and find each other deeply frustrating.

Open-source development breaks this bind, making it far easier for tester and developer to develop a shared representation grounded in the actual source code and to communicate effectively about it. Practically, there is a huge difference in leverage for the developer between the kind of bug report that just reports externally-visible symptoms and the kind that hooks directly to the developer's source-code–based mental representation of the program.

Most bugs, most of the time, are easily nailed given even an incomplete but suggestive characterization of their error conditions at source-code level. When someone among your beta-testers can point out, "there's a boundary problem in line nnn", or even just "under conditions X, Y, and Z, this variable rolls over", a quick look at the offending code often suffices to pin down the exact mode of failure and generate a fix.

Thus, source-code awareness by both parties greatly enhances both good communication and the synergy between what a beta-tester reports and what the core developer(s) know. In turn, this means that the core developers' time tends to be well conserved, even with many collaborators.

Another characteristic of the open-source method that conserves developer time is the communication structure of typical open-source projects. Above I used the term "core developer"; this reflects a distinction between the project core (typically quite small; a single core developer is common, and one to three is typical) and the project halo of beta-testers and available contributors (which often numbers in the hundreds).

The fundamental problem that traditional software-development organization addresses is Brook's Law: ``Adding more programmers to a late project makes it later.'' More generally, Brooks's Law predicts that the complexity and communication costs of a project rise with the square of the number of developers, while work done only rises linearly.

Brooks's Law is founded on experience that bugs tend strongly to cluster at the interfaces between code written by different people, and that communications/coordination overhead on a project tends to rise with the number of interfaces between human beings. Thus, problems scale with the number of communications paths between developers, which scales as the square of the humber of developers (more precisely, according to the formula N*(N - 1)/2 where N is the number of developers).

The Brooks's Law analysis (and the resulting fear of large numbers in development groups) rests on a hidden assummption: that the communications structure of the project is necessarily a complete graph, that everybody talks to everybody else. But on open-source projects, the halo developers work on what are in effect separable parallel subtasks and interact with each other very little; code changes and bug reports stream through the core group, and only within that small core group do we pay the full Brooksian overhead. [SU]

There are are still more reasons that source-code–level bug reporting tends to be very efficient. They center around the fact that a single error can often have multiple possible symptoms, manifesting differently depending on details of the user's usage pattern and environment. Such errors tend to be exactly the sort of complex and subtle bugs (such as dynamic-memory-management errors or nondeterministic interrupt-window artifacts) that are hardest to reproduce at will or to pin down by static analysis, and which do the most to create long-term problems in software.

A tester who sends in a tentative source-code–level characterization of such a multi-symptom bug (e.g. "It looks to me like there's a window in the signal handling near line 1250" or "Where are you zeroing that buffer?") may give a developer, otherwise too close to the code to see it, the critical clue to a half-dozen disparate symptoms. In cases like this, it may be hard or even impossible to know which externally-visible misbehaviour was caused by precisely which bug—but with frequent releases, it's unnecessary to know. Other collaborators will be likely to find out quickly whether their bug has been fixed or not. In many cases, source-level bug reports will cause misbehaviours to drop out without ever having been attributed to any specific fix.

Complex multi-symptom errors also tend to have multiple trace paths from surface symptoms back to the actual bug. Which of the trace paths a given developer or tester can chase may depend on subtleties of that person's environment, and may well change in a not obviously deterministic way over time. In effect, each developer and tester samples a semi-random set of the program's state space when looking for the etiology of a symptom. The more subtle and complex the bug, the less likely that skill will be able to guarantee the relevance of that sample.

For simple and easily reproducible bugs, then, the accent will be on the "semi" rather than the "random"; debugging skill and intimacy with the code and its architecture will matter a lot. But for complex bugs, the accent will be on the "random". Under these circumstances many people running traces will be much more effective than a few people running traces sequentially—even if the few have a much higher average skill level.

This effect will be greatly amplified if the difficulty of following trace paths from different surface symptoms back to a bug varies significantly in a way that can't be predicted by looking at the symptoms. A single developer sampling those paths sequentially will be as likely to pick a difficult trace path on the first try as an easy one. On the other hand, suppose many people are trying trace paths in parallel while doing rapid releases. Then it is likely one of them will find the easiest path immediately, and nail the bug in a much shorter time. The project maintainer will see that, ship a new release, and the other people running traces on the same bug will be able to stop before having spent too much time on their more difficult traces [RJ].

## 장미가 장미다우려면

(김동윤)Having studied Linus's behavior and formed a theory about why it was successful, I made a conscious decision to test this theory on my new (admittedly much less complex and ambitious) project.

리눅스의 행동을 연구하고 그것이 왜 성공적이었는지에 대한 이론을 만든 후, 나는 이 이론을 내 새로운 프로젝트 (물론 훨씬 덜 복잡하고 덜 야심적인 프로젝트) 에 적용해 보기로 했다.

But the first thing I did was reorganize and simplify popclient a lot. Carl Harris's implementation was very sound, but exhibited a kind of unnecessary complexity common to many C programmers. He treated the code as central and the data structures as support for the code. As a result, the code was beautiful but the data structure design ad-hoc and rather ugly (at least by the high standards of this veteran LISP hacker).

그러나 내가 가장 먼저 한 일은 popclient 를 더 재정리하고 단순화한 것이었다. 칼 해리스 (Carl Harris) 의 구현방식은 매우 괜찮았지만 많은 C 프로그래머들에게서 볼 수 있는 일종의 불필요한 복잡성을 보여주었다. 그는 코드를 중심으로 취급하고 자료구조는 코드를 받쳐주는 것으로 사용했다. 그 결과 코드는 아름답지만 자료구조는 임시변통(ad-hoc)으로 설계되었고, 보기에 좋지 않았다. (최소한 옛 LISP 해커의 높은 기준에서 보자면 말이다)

I had another purpose for rewriting besides improving the code and the data structure design, however. That was to evolve it into something I understood completely. It's no fun to be responsible for fixing bugs in a program you don't understand.

그러나 나는 코드와 자료구조를 개선하는 것 외에 또다른 목적을 가지고 있었다. 그것은 popclient를 내가 완전히 이해하는 무언가로 진화시키는 것이었다. 이해하지 못하는 프로그램의 버그를 수정하는 역할을 맡는 것은 재미가 없다.

(안창희)
For the first month or so, then, I was simply following out the implications of Carl's basic design. 

처음 한 달 정도를 나는 칼의 기본 설계가 시사하는 바를 그대로 따라다니기만 했다.

The first serious change I made was to add IMAP support. 

내가 처음 만든 중요한 변경점은 IMAP 지원을 추가하는 것이었다.

I did this by reorganizing the protocol machines into a generic driver and three method tables (for POP2, POP3, and IMAP). 

나는 이것을 프로토콜 머신을 일반 드라이버와 세 가지 메서드 테이블(POP2, POP3, IMAP용)으로 재구성하는 식으로 수행했다.

This and the previous changes illustrate a general principle that's good for programmers to keep in mind, especially in languages like C that don't naturally do dynamic typing:

이전의 변화와 더불어 이것은 프로그래머가 명심하기 좋은 일반적인 원리를 보여준다, 특히 C와 같이 자연스럽게 동적 타이핑을 지원하지 않는 언어에 있어서는 더 그렇다.
   
(박상혁)
9. Smart data structures and dumb code works a lot better than the other way around.

9. 자료구조를 훌륭하게 만들고 코드를 멍청하게 만드는 것이 그 반대의 경우보다 훨씬 잘 작동한다. (Smart data structures and dumb code works a lot better than the other way around)

Brooks, Chapter 9: **Show me your flowchart and conceal your tables, and I shall continue to be mystified. Show me your tables, and I won't usually need your flowchart; it'll be obvious.** Allowing for thirty years of terminological/cultural shift, it's the same point.

브룩스의 책 9장에 이렇게 쓰여 있다. **[자료구조]를 꽁꽁 숨긴다면, 내게 [코드]를 보여줄지언정 나는 계속 어리둥절할 것이다. 하지만 자료구조를 보여준다면, 나는 코드를 꼭 보려고 하지는 않을 것이다. 이는 명백한 사실이다.** 여기서 브룩스는 흐름도(flowchart)와 표(table)라고 표현했다. 다만 30년 동안의 용어와 문화의 변화를 고려한다면, 본질적인 의미는 같다고 할 수 있다.

At this point (early September 1996, about six weeks from zero) I started thinking that a name change might be in order—after all, it wasn't just a POP client any more. But I hesitated, because there was as yet nothing genuinely new in the design. My version of popclient had yet to develop an identity of its own.

일을 시작하고 6주가 지난 1996년 9월 초쯤, 나는 프로그램의 이름을 바꿔야 하는 것이 옳다고 생각했다. 이 프로그램이 더 이상 POP 클라이언트로서의 기능만을 수행하는 것이 아니었기 때문이다. 하지만 설계상으로 정말 새롭다고 할 만한 무언가가 아직 없었기 때문에, 나는 내 판단에 대해 확신하지 못하고 망설이고 있었다. 내가 만든 popclient 는 아직 스스로의 정체성을 확립하지 못하고 있었다.

That changed, radically, when popclient learned how to forward fetched mail to the SMTP port. I'll get to that in a moment. But first: I said earlier that I'd decided to use this project to test my theory about what Linus Torvalds had done right. How (you may well ask) did I do that? In these ways:

하지만 popclient가 가져온 메일을 어떻게 SMTP 포트로 전달해야 하는지 알고 난 후에는 상황이 급격히 바뀌었다. 이에 대해서는 잠시 후에 이야기하도록 하겠다. 그에 앞서, 나는 리누스 토발즈가 옳은 방법으로 일을 해냈다는 내 이론을 검증하기 위해 이 프로젝트를 수행하기로 했다고 말했다. 어떻게 검증했을까? 다음과 같은 방법을 사용했다.

* I released early and often (almost never less often than every ten days; during periods of intense development, once a day).
* 일찍, 자주 발표했다. (발표간격이 10일을 넘는 경우는 거의 없었으며 개발에 몰두해 있을 때는 하루에 한번씩 발표했다)

* I grew my beta list by adding to it everyone who contacted me about fetchmail.
* fetchmail 에 대한 일로 나에게 연락해 오는 사람은 누구든지 베타테스터 목록에 올렸다.

* I sent chatty announcements to the beta list whenever I released, encouraging people to participate.
* 새로 발표할 때마다 베타테스터들에게 떠들썩하게 발표를 알리며 사람들이 참여하도록 격려했다.

* And I listened to my beta-testers, polling them about design decisions and stroking them whenever they sent in patches and feedback.
* 그리고 그들의 이야기를 들었다. 설계 결정에 대해 투표를 하기도 했고 패치나 피드백을 보내올 때마다 베타테스터들을 구슬렀다.

(정세빈) The payoff from these simple measures was immediate. From the beginning of the project, I got bug reports of a quality most developers would kill for, often with good fixes attached. I got thoughtful criticism, I got fan mail, I got intelligent feature suggestions. Which leads to:

위의 단순한 방법들에 의한 효과는 즉각적으로 나타났다. 나는 프로젝트 시작부터 대부분의 개발자라면 죽일 만큼 갖고 싶어 할 품질의 버그 리포트를 받았는데, 때로는 리포트에 훌륭한 수정이 붙어있기도 하였다. 나는 사려 깊은 비판과 팬 메일, 지능적인 기능 제안들 또한 받았다. 여기서 다음과 같은 결론을 이끌어 낼 수 있었다.:

10\. If you treat your beta-testers as if they're your most valuable resource, they will respond by becoming your most valuable resource.

10\. 네가 베타테스터들을 너의 가장 가치 있는 자원처럼 대한다면, 그들은 너의 가장 가치 있는 자원이 되는 것으로 응답할 것이다.

One interesting measure of fetchmail's success is the sheer size of the project beta list, fetchmail-friends. At the time of latest revision of this paper (November 2000) it has 287 members and is adding two or three a week.

Fetchmail의 성공에 대한 흥미로운 척도 중 하나는 프로젝트 베타테스터 목록, fetchmail-friends의 크기였다. 이 글의 최근 개정 (2000년 11월) 당시에는 287명의 회원이 속해 있었고 일주일에 2~3 명이 추가되고 있었다.

Actually, when I revised in late May 1997 I found the list was beginning to lose members from its high of close to 300 for an interesting reason. Several people have asked me to unsubscribe them because fetchmail is working so well for them that they no longer need to see the list traffic! Perhaps this is part of the normal life-cycle of a mature bazaar-style project.

사실, 1997년 5월 말 경에 글을 수정했을 때, 나는 이 목록이 흥미로운 이유로 300명에 가까웠던 최고점에서 회원을 잃기 시작했다는 것을 발견하였다. 몇몇 사람들이 fetchmail이 잘 작동하기 때문에 더 이상 메일링리스트를 볼 필요가 없다며 나에게 구독 취소를 요청한 것이다! 아마 이것이 성숙한 바자-스타일(bazaar-style) 프로젝트가 가지는 정상적인 수명 주기의 일부일 것이다.


## Popclient becomes Fetchmail

## Popclient가 Fetchmail이 되다.

The real turning point in the project was when Harry Hochheiser sent me his scratch code for forwarding mail to the client machine's SMTP port. I realized almost immediately that a reliable implementation of this feature would make all the other mail delivery modes next to obsolete.

fetchmail 프로젝트에서 큰 전환이 일어났던 것은 해리 호흐하이저(Harry Hochheiser) 가 클라이언트 머신의 SMTP 포트로 메일을 포워딩하는 대략적인 코드를 보내준 때였다. 보자마자 이 기능을 안정적으로 구현한다면 다른 모든 배달 방법은 구식이 되리라는 것을 깨달았다.

For many weeks I had been tweaking fetchmail rather incrementally while feeling like the interface design was serviceable but grubby—inelegant and with too many exiguous options hanging out all over. The options to dump fetched mail to a mailbox file or standard output particularly bothered me, but I couldn't figure out why. (If you don't care about the technicalia of Internet mail, the next two paragraphs can be safely skipped.)

여러 주 동안 나는 fetchmail을 조금씩 뜯어고치고 있었는데, 인터페이스 설계가 작동하긴 하지만 지저분하다고 느끼고 있었다. 우아하지도 않고 몇 안되는 옵션들이 너무 여기저기 흩어져 있었다. 가져온 메일을 메일박스 파일에 부어놓을 것인지, 표준출력으로 내보낼 것인지 결정하는 옵션이 특히 골치거리였지만 왜 그런지 확실히 깨닫지는 못했다.

What I saw when I thought about SMTP forwarding was that popclient had been trying to do too many things. It had been designed to be both a mail transport agent (MTA) and a local delivery agent (MDA). With SMTP forwarding, it could get out of the MDA business and be a pure MTA, handing off mail to other programs for local delivery just as sendmail does.

SMTP 포워딩을 생각하자 그동안 popclient 가 너무 많은 것을 해내려고 했다는 것을 알게 되었다. poopclient 는 MTA (Mail Transport Agent) 와 MDA (Mail Delivery Agent)의 기능을 모두 가지도록 설계되었다. SMTP 포워딩만 할 수 있다면 MDA 기능을 없애 순수한 MTA 가 될 수 있었다. sendmail 과 마찬가지로 최종적인 메일 배달은 다른 프로그램에게 맡기면 되는 것이다.

Why mess with all the complexity of configuring a mail delivery agent or setting up lock-and-append on a mailbox when port 25 is almost guaranteed to be there on any platform with TCP/IP support in the first place? Especially when this means retrieved mail is guaranteed to look like normal sender-initiated SMTP mail, which is really what we want anyway. (Back to a higher level....)

TCP/IP를 지원하는 플랫폼이라면 거의 어디에나 25번 포트가 기다리고 있는데 무엇 때문에 복잡한 MDA 기능을 설정하거나 메일박스를 잠그고 덧붙이는 (lock-and-append) 문제를 가지고 고생을 하는가? 더구나 포워딩을 사용하면 가져온 메일이 평범한 SMTP 메일처럼 보일 것이고, 우리가 원하는 것이 바로 그것이었는데 말이다.

Even if you didn't follow the preceding technical jargon, there are several important lessons here. First, this SMTP-forwarding concept was the biggest single payoff I got from consciously trying to emulate Linus's methods. A user gave me this terrific idea—all I had to do was understand the implications.

몇가지 배울 점이 있었다. 먼저, SMTP 포워딩에 대한 아이디어는 내가 리누스의 방법을 모방하려고 의식적으로 노력한 것에 대한 가장 큰 보답이었다. 사용자 한 명이 내게 끝내주는 아이디어를 주었으며 내가 해야했던 일은 그 의미를 이해하는 것 뿐이었다.

11. The next best thing to having good ideas is recognizing good ideas from your users. Sometimes the latter is better.

11. 좋은 아이디어를 생각해내는 것 다음으로 중요한 일은 사용자들이 알려준 좋은 아이디어를 깨닫는 것이다. 때로는 이편이 더 나을 수도 있다. (The next best thing to having good ideas is recognizing good ideas from your users. Sometimes the latter is better)

Interestingly enough, you will quickly find that if you are completely and self-deprecatingly truthful about how much you owe other people, the world at large will treat you as though you did every bit of the invention yourself and are just being becomingly modest about your innate genius. We can all see how well this worked for Linus!

흥미롭게도 만일 당신이 얼마나 다른사람에게 빚을 많이 지고 있는지를 자기 비하라고 느껴질 정도로까지 솔직하게 털어놓는다면 대개의 사람들은 당신이 혼자서 거의 모든 일을 해내고서 천재성에 대해서 겸손해 하는 것처럼 대한다는 것을 곧바로 알게 될 것이다. 리누스의 경우를 보라! 

(When I gave my talk at the first Perl Conference in August 1997, hacker extraordinaire Larry Wall was in the front row. As I got to the last line above he called out, religious-revival style, ``Tell it, tell it, brother!''. The whole audience laughed, because they knew this had worked for the inventor of Perl, too.)

(1997년 8월, Perl 컨퍼런스에서 이 글을 발표할 때 래리 월이 첫 번째 줄에 앉아 있었다. 바로 윗 줄에 도달했을 때 그는 부흥사라도 된 것처럼 외쳤다. ``형제여, 이야기 하시오, 이야기를!'' 청중들 모두가 이것이 Perl을 만든 래리에게도 적용된다는 것을 알았기 때문에 웃음을 터뜨렸다.)

After a very few weeks of running the project in the same spirit, I began to get similar praise not just from my users but from other people to whom the word leaked out. I stashed away some of that email; I'll look at it again sometime if I ever start wondering whether my life has been worthwhile :-).

똑같은 정신으로 프로젝트를 몇 주 진행해 나가자 나는 사용자들 뿐 아니라 이야기를 전해들은 다른 사람들로부터 비슷한 칭송을 받기 시작했다. 나는 그런 email 중 몇몇을 따로 보관해 두었다. 나중에 내 삶이 가치있는 것이었는지 의심스러워질 때 그 메일들을 다시 꺼내볼 생각이다. :-)

But there are two more fundamental, non-political lessons here that are general to all kinds of design.

모든 종류의 설계에 대해서 적용될 수 있는 두가지 더 기본적이며 비정치적인 교훈이 있다.

12. Often, the most striking and innovative solutions come from realizing that your concept of the problem was wrong.

12. 종종 가장 충격적이고 혁신적인 해결책은 당신 자신이 문제에 대해서 가지고 있는 개념이 잘못되어 있다는 것을 깨닫는 것에서 나온다. (Often, the most striking and innovative solutions come from realizing that your concept of the problem was wrong)

I had been trying to solve the wrong problem by continuing to develop popclient as a combined MTA/MDA with all kinds of funky local delivery modes. Fetchmail's design needed to be rethought from the ground up as a pure MTA, a part of the normal SMTP-speaking Internet mail path.

나는 popclient를 MTA/MDA 기능을 다 갖추고 복잡한 지역배달모드들까지(local delivery modes) 갖춘 것으로 개발해 나가면서 틀린 문제를 풀려고 노력하고 있었다. fetchmail의 설계는 가장 기초적인 것부터 재고하여 SMTP 포트로 메일을 배달하는 인터넷 메일 경로의 한 부분인 순수 MTA 가 되어야 했다.

When you hit a wall in development—when you find yourself hard put to think past the next patch—it's often time to ask not whether you've got the right answer, but whether you're asking the right question. Perhaps the problem needs to be reframed.

개발 도중에 벽에 부딪친다면 - 다음번 패치 후에 무엇을 해야 할 지 모르겠다면 - 그때는 정답을 가지고 있는지 생각할 것이 아니라 질문이 올바른 것인지 의문을 가져보아야 하는 경우가 종종 있다. 아마도 문제의 틀을 다시 잡아야 할 것이다.

Well, I had reframed my problem. Clearly, the right thing to do was (1) hack SMTP forwarding support into the generic driver, (2) make it the default mode, and (3) eventually throw out all the other delivery modes, especially the deliver-to-file and deliver-to-standard-output options.

그래서, 나도 내 문제의 틀을 다시 잡았다. 분명히 제대로 일을 진행하려면 (1) SMTP 포워딩 지원 기능을 일반 드라이버에 포함시키고, (2) SMTP 포워딩을 기본모드로 만들고 (3) 최종적으로는 다른 배달모드들, 특히 `파일로 배달하기' 와 `표준출력으로 배달하기'를 제거해야 했다.'

I hesitated over step 3 for some time, fearing to upset long-time popclient users dependent on the alternate delivery mechanisms. In theory, they could immediately switch to .forward files or their non-sendmail equivalents to get the same effects. In practice the transition might have been messy.

나는 단계 (3)에서 조금 머뭇거렸는데, 이유는 오랫동안 popclient 를 써오면서 다른 배달모드에 의존하고 있을 사용자들의 심기를 불편하게 만들고 싶지 않았기 때문이다. 이론적으로는 그들 모두 즉시 .forward 파일이나 sendmail 외의 비슷한 프로그램으로 전환하여 동일한 결과를 얻을 수 있었다. 실제로는 전환 자체가 큰 일이 될 것이었다.

But when I did it, the benefits proved huge. The cruftiest parts of the driver code vanished. Configuration got radically simpler—no more grovelling around for the system MDA and user's mailbox, no more worries about whether the underlying OS supports file locking.

하지만 단계 (3)을 실행하고 나자 이점이 매우 큰 것으로 나타났다. 드라이버 코드 중 가장 힘든 부분이 사라졌다. 설정이 엄청나게 간단해졌다 - 시스템의 MDA 와 사용자의 메일박스를 일일이 찾아다니며 굽실거릴 필요도 없어졌고, OS 가 파일 잠금을 지원하는지 걱정할 필요도 없어졌다.

Also, the only way to lose mail vanished. If you specified delivery to a file and the disk got full, your mail got lost. This can't happen with SMTP forwarding because your SMTP listener won't return OK unless the message can be delivered or at least spooled for later delivery.

게다가 메일을 잃어버릴 한가지 가능성도 사라졌다. `파일로 배달하기'를 선택했을 때 디스크가 꽉 차 있으면 메일이 사라져 버렸던 것이다. SMTP 포워딩에서는 SMTP 리스너가 메시지 배달이 가능하거나 나중에 배달할 수 있도록 스풀해 놓기 전에는 OK를 돌려주지 않을 것이기 때문에 이런 일이 일어날 수가 없다.

Also, performance improved (though not so you'd notice it in a single run). Another not insignificant benefit of this change was that the manual page got a lot simpler.

성능도 향상되었다(한두번 실행시켜서는 느끼지 못하겠지만). 또 변경에 따르는 그다지 중요하지 않은 이익이라면 매뉴얼 페이지가 훨씬 간단해 졌다는 것이다. 

Later, I had to bring delivery via a user-specified local MDA back in order to allow handling of some obscure situations involving dynamic SLIP. But I found a much simpler way to do it.

나중에 나는 사용자가 지정한 지역 MDA를 통해 배달하는 기능을 다시 넣어야 했다. 동적인 SLIP를 포함하여 몇몇 애매한 상황을 다루어야 했기 때문이다. 하지만 처음보다 훨씬 간단한 방법을 찾아낼 수 있었다.

The moral? Don't hesitate to throw away superannuated features when you can do it without loss of effectiveness. Antoine de Saint-Exupéry (who was an aviator and aircraft designer when he wasn't authoring classic children's books) said:

교훈이라면? 낡아서 사용할 수 없는 기능이라면 효율을 떨어뜨리지 않고 제거할 수 있을 때는 망설이지 말고 제거해 버리라. 앙뜨완 드 생떽쥐뻬리는 (아동서적 작가였으며 남는 시간에는 비행기 조종과 설계를 했던) 이렇게 말했다.

13. ``Perfection (in design) is achieved not when there is nothing more to add, but rather when there is nothing more to take away.''

13. ``(설계에 있어서) 완벽함이란 더 이상 추가할 것이 없을 때 이루어지는 것이 아니라 더 이상 버릴 것이 없을 때 이루어진다. (Perfection (in design) is achieved not when there is nothing more to add, but rather when there is nothing more to take away)''

When your code is getting both better and simpler, that is when you know it's right. And in the process, the fetchmail design acquired an identity of its own, different from the ancestral popclient.

코드가 더 나아지고 간단해지고 있을 때가 바로 일이 제대로 되어가고 있다는 것을 알게 되는 때다. 그리고 그 과정에서 fetchmail 의 설계는 그 조상격인 popclient 와 다른, 자신만의 정체성을 획득했다. 

It was time for the name change. The new design looked much more like a dual of sendmail than the old popclient had; both are MTAs, but where sendmail pushes then delivers, the new popclient pulls then delivers. So, two months off the blocks, I renamed it fetchmail.

이름을 바꿀 때가 된 것이다. 새로운 설계는 예전의 popclient 보다는 sendmail 과 비슷해 보였다. 둘다 MTA였으나 sendmail은 푸시(push) 후에 메일을 배달했고 새로운 popclient 는 풀(pull) 후에 메일을 배달했다. 해서 두 달 후에 나는 popclient 의 이름을 fetchmail로 변경했다.

There is a more general lesson in this story about how SMTP delivery came to fetchmail. It is not only debugging that is parallelizable; development and (to a perhaps surprising extent) exploration of design space is, too. When your development mode is rapidly iterative, development and enhancement may become special cases of debugging—fixing `bugs of omission' in the original capabilities or concept of the software.

Even at a higher level of design, it can be very valuable to have lots of co-developers random-walking through the design space near your product. Consider the way a puddle of water finds a drain, or better yet how ants find food: exploration essentially by diffusion, followed by exploitation mediated by a scalable communication mechanism. This works very well; as with Harry Hochheiser and me, one of your outriders may well find a huge win nearby that you were just a little too close-focused to see.

## Fetchmail Grows Up

## Fetchmail 의 성장

There I was with a neat and innovative design, code that I knew worked well because I used it every day, and a burgeoning beta list. It gradually dawned on me that I was no longer engaged in a trivial personal hack that might happen to be useful to few other people. I had my hands on a program that every hacker with a Unix box and a SLIP/PPP mail connection really needs.

이제는 깔끔하고 혁신적인 설계, 매일 사용하므로 잘 작동하는 것을 알고 있는 코드, 발전하고 있는 베타테스터의 목록을 가지고 있었다. 더이상 내가 하고 있는 일이 몇몇의 사람에게 유용할 수도 있는 사소하고 개인적인 해킹은 아니라는 생각이 서서히 들기 시작했다. 내가 가지고 있는 것은 유닉스 박스와 SLIP/PPP 메일 연결을 가지고 있는 모든 해커들이 정말로 필요로 하는 프로그램이었다.

With the SMTP forwarding feature, it pulled far enough in front of the competition to potentially become a ``category killer'', one of those classic programs that fills its niche so competently that the alternatives are not just discarded but almost forgotten.

SMTP 포워딩 기능으로 fetchmail 은 경쟁에서 멀찍이 앞서나와 ``카테고리 킬러,'' 그러니까 해당분야의 다른 프로그램들은 아예 잊혀져 버릴 만한 경쟁력을 갖추고 자신의 지위를 확고하게 하는 고전적인 프로그램이 될 수 있는 능력을 갖추었다.

I think you can't really aim or plan for a result like this. You have to get pulled into it by design ideas so powerful that afterward the results just seem inevitable, natural, even foreordained. The only way to try for ideas like that is by having lots of ideas—or by having the engineering judgment to take other peoples' good ideas beyond where the originators thought they could go.

이런 결과를 계획하거나 목표로 가질 수는 없으리라고 생각한다. 아주 강력한 설계상의 아이디어로 그런 결과가 불가피하고, 자연스러우며 운명적인 것으로 보이게 함으로써 그런 결과에 도달해야 한다. 그런 아이디어를 구체화해 볼 수 있는 유일한 방법은 수많은 아이디어를 가지는 것이다. 아니면 다른 사람들의 좋은 아이디어를, 원래 생각되었던 것보다 더 멀리 이끌고 가서 구체화 시켜보는 방법이다.

Andy Tanenbaum had the original idea to build a simple native Unix for IBM PCs, for use as a teaching tool (he called it Minix). Linus Torvalds pushed the Minix concept further than Andrew probably thought it could go—and it grew into something wonderful. In the same way (though on a smaller scale), I took some ideas by Carl Harris and Harry Hochheiser and pushed them hard. Neither of us was `original' in the romantic way people think is genius. But then, most science and engineering and software development isn't done by original genius, hacker mythology to the contrary.

앤드류 타넨바움 (Andrew Tanenbaum) 은 교습 도구로 사용하기 위해 386용으로 간단한 네이티브 유닉스를 만들려는 원래의 아이디어를 가지고 있었다. 리누스 토발즈는 이 미닉스의 개념을 앤드류가 생각했던 것보다 더 멀리 밀고 나갔다. 그래서 리눅스는 굉장한 것이 되었다. 똑같은 방식으로 (더 작은 스케일이었지만) 나는 칼 해리스와 해리 호흐하이저의 아이디어들을 가져와 강하게 밀어붙였다. 리누스나 나나 사람들이 천재들이 그러하리라고 생각하는 낭만적인 의미에서 `독창적' 인 것은 아니었다. 하지만 통념과는 반대로 대부분의 과학과 공학과 소프트웨어 개발은 독창적인 천재, 해커의 전설에 의해서 이루어지지는 않는다.

The results were pretty heady stuff all the same—in fact, just the kind of success every hacker lives for! And they meant I would have to set my standards even higher. To make fetchmail as good as I now saw it could be, I'd have to write not just for my own needs, but also include and support features necessary to others but outside my orbit. And do that while keeping the program simple and robust.

결과물은 똑같이 매우 사람을 흥분시키는 것들이다 -- 사실, 모든 해커들은 이런 종류의 성공을 얻기 위해 살아간다! 거기에는 내가 기준을 더 높이 잡아야 한다는 의미도 들어있다. fetchmail을 최상의 것으로 만들기 위해 나는 내자신의 필요뿐 아니라 나와는 상관없지만 다른 사람들에게는 필수적인 기능을 포함시키고 지원해야했다. 게다가 프로그램을 단순하고 튼튼하게 유지시키면서 그런 일을 해야 했다.

The first and overwhelmingly most important feature I wrote after realizing this was multidrop support—the ability to fetch mail from mailboxes that had accumulated all mail for a group of users, and then route each piece of mail to its individual recipients.

이것을 깨닫고 나서 내가 추가한 매우 중요한 첫 번째 기능은 멀티드롭(multidrop) 기능이었다. 그룹이나 사용자들의 메일을 한꺼번에 가지고 있는 메일박스에서 메일을 가져와 각 메일들을 개인 수신자에게 라우트(route) 시켜주는 기능이었다.

I decided to add the multidrop support partly because some users were clamoring for it, but mostly because I thought it would shake bugs out of the single-drop code by forcing me to deal with addressing in full generality. And so it proved. Getting RFC 822 address parsing right took me a remarkably long time, not because any individual piece of it is hard but because it involved a pile of interdependent and fussy details.

멀티드롭 기능을 추가하기로 한 데에는 몇몇 사용자들이 원한다는 것도 있었지만 가장 큰 이유는 어드레싱을 완전히 구현함으로써 싱글드롭 코드에 있는 버그들을 잡아낼 수 있으리라고 생각했기 때문이다. 그리고 그렇게 되었다. RFC 822의 파싱을 제대로 구현하는 것에 매우 오랜 시간이 걸렸는데, 각각의 조각이 어려웠기 때문이 아니라 각각이 서로 의존하고 있으며 세심하게 신경을 써야 하는 사항들이었기 때문이다. 

But multidrop addressing turned out to be an excellent design decision as well. Here's how I knew:

하지만 멀티드롭 어드레싱 역시 매우 훌륭한 설계상의 결정이었던 것으로 드러났다. 다음과 같은 교훈을 얻을 수 있었다.

14. Any tool should be useful in the expected way, but a truly great tool lends itself to uses you never expected.

14. 어떤 도구든지 기대하는 방법으로 쓸모가 있어야 하지만 정말 위대한 도구는 사용자가 전혀 기대하지 않았던 용도에 알맞게 된다. (Any tool should be useful in the expected way, but a truly great tool lends itself to uses you never expected)

The unexpected use for multidrop fetchmail is to run mailing lists with the list kept, and alias expansion done, on the client side of the Internet connection. This means someone running a personal machine through an ISP account can manage a mailing list without continuing access to the ISP's alias files.

미처 생각하지 못했던 멀티드롭 fetchmail 의 용도는 메일링리스트를 그대로 유지한 채, 알리아스 확장이 된 채로 SLIP/PPP로 연결된 클라이언트 쪽에서 메일링리스트를 운영하는 것이었다. 개인의 컴퓨터로 ISP 계정을 통해 접속하는 사람이 ISP 의 알리아스 파일에 지속적으로 접근하지 않고도 메일링리스트를 운영할 수 있다는 것을 의미한다.

Another important change demanded by my beta-testers was support for 8-bit MIME (Multipurpose Internet Mail Extensions) operation. This was pretty easy to do, because I had been careful to keep the code 8-bit clean (that is, to not press the 8th bit, unused in the ASCII character set, into service to carry information within the program). Not because I anticipated the demand for this feature, but rather in obedience to another rule:

베타테스터들이 요구한 중요한 변경사항중 또 하나는 8비트 MIME 오퍼레이션이었다. 이것은 내가 코드를 8비트에 대비하여 계속 유지시켜왔기 때문에 매우 쉬운 일이었다. 이런 기능에 대한 요구를 미리 예측해서 그랬던 것은 아니다. 다음과 같은 규칙을 따르려고 해서였다.

15. When writing gateway software of any kind, take pains to disturb the data stream as little as possible—and never throw away information unless the recipient forces you to!

15. 어떤 종류든 게이트웨이 소프트웨어를 만들려고 한다면 데이터 스트림에 가능한 한 최소한의 조작만 가하라 -- 그리고 수신자가 강제로 하게 하지 않는다면 정보를 *절대로* 잘라버리지 말라. (When writing gateway software of any kind, take pains to disturb the data stream as little as possible -- and *never* throw away informtion unless the recipient forces you to!)

Had I not obeyed this rule, 8-bit MIME support would have been difficult and buggy. As it was, all I had to do is read the MIME standard (RFC 1652) and add a trivial bit of header-generation logic.

이 규칙을 따르지 않았다면 8비트 MIME 지원은 매우 어려웠을 것이며 많은 버그를 만들어 냈으리라. 규칙을 따랐기 때문에 내가 해야 할 일은 RFC 1652 를 읽고 헤더 생성 로직을 약간 수정하는 것 뿐이었다.

Some European users bugged me into adding an option to limit the number of messages retrieved per session (so they can control costs from their expensive phone networks). I resisted this for a long time, and I'm still not entirely happy about it. But if you're writing for the world, you have to listen to your customers—this doesn't change just because they're not paying you in money.

유럽의 몇몇 사용자들은 한 세션에서 가져올 수 있는 메시지의 수를 제한하도록 옵션을 추가해달라고 요구해왔다.(전화 네트워크의 비싼 비용을 조절할 수 있도록 해달라는 말이다) 오랫동안 여기에 저항했고, 아직도 완전히 수긍하지 못했다. 하지만 세계를 상대로 프로그램을 만든다면 고객들의 소리에 귀를 기울여야 한다 -- 그들이 돈을 지불하지 않는다고 해도 마찬가지다.

## A Few More Lessons from Fetchmail

## Fetchmail에서 배울 점

Before we go back to general software-engineering issues, there are a couple more specific lessons from the fetchmail experience to ponder. Nontechnical readers can safely skip this section.

일반적인 소프트웨어공학의 주제로 돌아가기 전에 fetchmail의 경험으로부터 배울 점이 몇 가지 더 있다. rc 파일의 구문은 선택사항으로 `noise' 라는 키워드를 포함하는데 이것은 파서에 의해 무시된다. rc 파일에서 허용하는 영어와 비슷한 구문은 잘라낼 것을 모두 잘라낸 후에 얻는 전통적이고 간명한 키워드-밸류 짝에 비해 훨씬 알아보기 쉽다.

The rc (control) file syntax includes optional `noise' keywords that are entirely ignored by the parser. The English-like syntax they allow is considerably more readable than the traditional terse keyword-value pairs you get when you strip them all out.

이것은 내가 rc 파일의 선언들이 명령형 소언어 (imperative minilanguage)를 얼마나 많이 닮아가기 시작했는지 알아차리고 나서 한밤중의 실험으로 시작되었다. (popclient 의 `server' 라는 키워드를 `poll' 로 바꾼 이유도 이것이다)

These started out as a late-night experiment when I noticed how much the rc file declarations were beginning to resemble an imperative minilanguage. (This is also why I changed the original popclient ``server'' keyword to ``poll'').

명령형 소언어를 더 영어처럼 만들면 사용하기 쉬울 것으로 보였다. 지금은 내가 비록 Emacs 나 HTML, 그리고 많은 데이터베이스 엔진에서 볼 수 있듯이 설계를 할 때 ``언어처럼 만드는'' 파의 일원이긴 하지만 ``영어와 비슷한'' 구분을 가지는 것에 대해서는 그다지 달가와 하지 않는다.

It seemed to me that trying to make that imperative minilanguage more like English might make it easier to use. Now, although I'm a convinced partisan of the ``make it a language'' school of design as exemplified by Emacs and HTML and many database engines, I am not normally a big fan of ``English-like'' syntaxes.

Traditionally programmers have tended to favor control syntaxes that are very precise and compact and have no redundancy at all. This is a cultural legacy from when computing resources were expensive, so parsing stages had to be as cheap and simple as possible. English, with about 50% redundancy, looked like a very inappropriate model then.

전통적인 프로그래머들은 정확하고 짧으며 중복을 허용하지 않는 제어구문을 선호하는 경향이 있다. 이것은 컴퓨팅 자원이 비싸서 파싱하는 단계가 최대한 싸고 간단해야 했을 때부터 내려온 문화적 유산이다. 영어는 대략 50% 정도의 중복을 허용하므로 대단히 부적절한 모델인 것으로 보인다.

This is not my reason for normally avoiding English-like syntaxes; I mention it here only to demolish it. With cheap cycles and core, terseness should not be an end in itself. Nowadays it's more important for a language to be convenient for humans than to be cheap for the computer.

이것이 내가 영어와 비슷한 구문을 일반적으로 피하는 이유는 아니다. 이 문제를 언급한 이유는 그런 관습을 없애기 위해서다. 사이클과 코어의 값이 싸졌는데도 간명함은 저절로 없어지지는 않았다. 최근에는 언어가 컴퓨터의 관점에서 싼 가격이라는 점보다는 사람에게 편리한가 하는 점이 더 중요하다.

There remain, however, good reasons to be wary. One is the complexity cost of the parsing stage—you don't want to raise that to the point where it's a significant source of bugs and user confusion in itself. Another is that trying to make a language syntax English-like often demands that the ``English'' it speaks be bent seriously out of shape, so much so that the superficial resemblance to natural language is as confusing as a traditional syntax would have been. (You see this bad effect in a lot of so-called ``fourth generation'' and commercial database-query languages.)

물론 조심해야 할 이유는 충분히 있다. 한 가지는 파싱하는 단계의 복잡성에 대한 비용이다 -- 파싱하는 단계를 버그가 우글거리는 데다가 사용자로 하여금 그 자체만으로 혼란을 일으키게 만들고 싶지는 않을 것이다. 또 하나의 이유는 언어의 구문을 영어와 비슷하게 만들려고 노력하면 그 ``영어'' 가 심각하게 왜곡되어 자연어와의 피상적인 유사점이 전통적인 구문만큼이나 혼란스럽게 되는 경우가 많다는 점이다. (소위 ``4세대'' 언어와 상업용 데이터베이스 질의어에서 이런 경우를 많이 볼 수 있다)

The fetchmail control syntax seems to avoid these problems because the language domain is extremely restricted. It's nowhere near a general-purpose language; the things it says simply are not very complicated, so there's little potential for confusion in moving mentally between a tiny subset of English and the actual control language. I think there may be a broader lesson here:

fetchmail 제어구문은 이런 문제를 피하려고 했다. 언어의 영역이 매우 제한되어 있었기 때문이다. 일반적인 목적의 언어와는 거리가 멀었다. 언어가 표현하는 것이 별로 복잡하지 않았기 때문에 영어의 일부분에서 실제 제어언어로 옮겨가는데 혼란을 일으킬 가능성이 적었다. 더 넓은 의미의 교훈을 여기에서 얻었다.

16. When your language is nowhere near Turing-complete, syntactic sugar can be your friend.

16. 언어가 튜링-컴플리트하지 않다면 구문상의 유연성이 필요하다. (When your language is nowhere near Turing-complete, syntactic sugar can be your friend)

Another lesson is about security by obscurity. Some fetchmail users asked me to change the software to store passwords encrypted in the rc file, so snoopers wouldn't be able to casually see them.

또하나의 교훈은 불투명함에 의한 보안에 대해서이다. fetchmail 의 사용자 중에는 스누퍼들이 우연히 패스워드를 보지 못하도록 rc 파일에 있는 패스워드를 암호화하여 가지고 있게 하자고 이야기하는 사람들이 있었다.

I didn't do it, because this doesn't actually add protection. Anyone who's acquired permissions to read your rc file will be able to run fetchmail as you anyway—and if it's your password they're after, they'd be able to rip the necessary decoder out of the fetchmail code itself to get it.

나는 그 이야기를 받아들이지 않았는데, 그렇게 한다고 해서 보안이 강화되는 것이 아니기 때문이다. rc 파일의 읽기 퍼미션을 얻은 사람이라면 사용자와 마찬가지로 fetchmail을 실행시킬 수도 있는 것이다 -- 그리고 그들이 패스워드를 원하는 것이라면 패스워드를 얻기 위해 fetchmail 코드에서 디코딩하는 코드를 뽑아낼 수도 있다.

All .fetchmailrc password encryption would have done is give a false sense of security to people who don't think very hard. The general rule here is:

.fetchmailrc 의 패스워드를 암호화 했다면 사람들은 그리 심각하게 생각하지도 않고 보안에 대해 잘못된 관념을 가지게 되었을 것이다. 여기서 알 수 있는 일반적인 규칙은 다음과 같다.

17. A security system is only as secure as its secret. Beware of pseudo-secrets.

17. 보안시스템은 그것이 보호하려고 하는 비밀만큼만 안전하다. 가짜 비밀들에 주의할 것. (A security system is only as secure as its secret. Beware of pseudo-secrets) 

## Necessary Preconditions for the Bazaar Style

## 시장 스타일의 개발에 필요한 선행조건들

Early reviewers and test audiences for this essay consistently raised questions about the preconditions for successful bazaar-style development, including both the qualifications of the project leader and the state of code at the time one goes public and starts to try to build a co-developer community.

이 글을 초기에 검토해준 사람들과 시험적으로 청중이 되었던 사람들은 계속해서 성공적인 시장 스타일의 개발을 위한 선행조건이 무엇인지 물었다. 여기에는 공동 개발자의 공동체를 만들기 위해 프로젝트가 공개되는 시점에 리더의 자질과 코드가 어떤 상태인지가 포함된다.

It's fairly clear that one cannot code from the ground up in bazaar style [IN]. One can test, debug and improve in bazaar style, but it would be very hard to originate a project in bazaar mode. Linus didn't try it. I didn't either. Your nascent developer community needs to have something runnable and testable to play with.

아예 처음부터 시장 스타일로 개발할 수 없다는 것은 자명하다. 테스트, 디버그, 그리고 개선은 시장 스타일로 할 수 있다. 하지만 프로젝트를 시작할 때 시장 스타일로 시작하기는 매우 어렵다. 리누스는 그렇게 하지 않았다. 나도 마찬가지. 개발자들의 공동체는 초기에 실행시키면서 테스트할 수 있는 장난감이 필요하다.

When you start community-building, what you need to be able to present is a plausible promise. Your program doesn't have to work particularly well. It can be crude, buggy, incomplete, and poorly documented. What it must not fail to do is (a) run, and (b) convince potential co-developers that it can be evolved into something really neat in the foreseeable future.

공동체를 만들기 시작할 때 제시할 수 있어야 하는 것은 그럴듯한 장래성이다. 프로그램이 특별히 잘 작동할 필요는 없다. 조잡하거나, 버그투성이여도 되고, 완성되지 않고 문서가 형편없어도 상관없다. 하지만 한가지 확실하게 해야할 것은 잠재적인 공동개발자들에게 이것이 머지 않은 미래에 정말 괜찮은 무언가로 진화할 수 있다는 것을 납득시키는 일이다.

Linux and fetchmail both went public with strong, attractive basic designs. Many people thinking about the bazaar model as I have presented it have correctly considered this critical, then jumped from that to the conclusion that a high degree of design intuition and cleverness in the project leader is indispensable.

리눅스와 fetchmail 둘 다 강력하고 매력적인 기본설계를 가지고 공개되었다. 내가 시장모델에 대해 이야기하자 많은 사람들이 이것을 중요하다고 생각했고, 높은 수준의 설계에 대한 직관과 영리함이 프로젝트의 리더에게는 필수적인 것이라고 지레짐작으로 결론을 내려버렸다.

But Linus got his design from Unix. I got mine initially from the ancestral popclient (though it would later change a great deal, much more proportionately speaking than has Linux). So does the leader/coordinator for a bazaar-style effort really have to have exceptional design talent, or can he get by through leveraging the design talent of others?

하지만 리누스는 그의 설계를 유닉스에서 따왔고 나는 기본적으로 popclient에서 가져왔다. (물론 나중에 많은 것이 바뀌긴 했지만 리눅스는 그보다 훨씬 더 바뀌었다). 그렇다면 시장스타일의 리더/조정자에게 정말 특별한 설계의 재능이 필요한 것일까, 아니면 다른 사람들이 가진 설계의 재능을 이끌어 내는 것이 필요한 것일까?

I think it is not critical that the coordinator be able to originate designs of exceptional brilliance, but it is absolutely critical that the coordinator be able to recognize good design ideas from others.

나는 조정자가 특별하게 영리해서 독창적인 설계를 만들어낼 수 있는지의 여부가 중요하다고 생각하지 않는다. 하지만 조정자가 다른사람의 좋은 설계를 알아볼 수 있는지 는 절대적으로 중요하다.

Both the Linux and fetchmail projects show evidence of this. Linus, while not (as previously discussed) a spectacularly original designer, has displayed a powerful knack for recognizing good design and integrating it into the Linux kernel. And I have already described how the single most powerful design idea in fetchmail (SMTP forwarding) came from somebody else.

리눅스와 fetchmail 프로젝트는 이에 대한 증거를 보여주고 있다. 리누스는 (앞서 논했듯이) 대단히 독창적인 설계자라고는 할 수 없으나 좋은 설계를 알아보는 대단한 요령을 보여주었고, 그것을 리눅스 커널에 통합해 넣었다. 앞서 fetchmail에서 가장 강력한 설계상의 아이디어 하나 (SMTP 포워딩)가 다른 누군가로부터 온 것이라고 설명한 바 있다.

Early audiences of this essay complimented me by suggesting that I am prone to undervalue design originality in bazaar projects because I have a lot of it myself, and therefore take it for granted. There may be some truth to this; design (as opposed to coding or debugging) is certainly my strongest skill.

이 글의 초기 청중들은 내가 설계상의 독창성을 과소평가하는데 내가 그런 독창성을 가지고 있기 때문에 당연한 일로 생각한다고 주장하며 내게 경의를 표시했다. 어느 정도는 사실이다. 설계는 분명히 (코딩이나 디버깅에 비해서) 내가 가장 잘하는 일이다.

But the problem with being clever and original in software design is that it gets to be a habit—you start reflexively making things cute and complicated when you should be keeping them robust and simple. I have had projects crash on me because I made this mistake, but I managed to avoid this with fetchmail.

하지만 소프트웨어 설계에 있어서 똑똑하고 독창적이라는 것의 문제점은 그게 버릇이 되어버린다는 점이다 -- 설계를 강력하고 단순하게 유지해야 할 때 그렇게 하지 않고 계속해서 일을 멋지고 복잡하게 만들기 시작한다. 이전의 프로젝트에서 나는 그런 성향 때문에 실패한 적이 있었다. fetchmail 에서는 간신히 그것을 이겨냈다.

So I believe the fetchmail project succeeded partly because I restrained my tendency to be clever; this argues (at least) against design originality being essential for successful bazaar projects. And consider Linux. Suppose Linus Torvalds had been trying to pull off fundamental innovations in operating system design during the development; does it seem at all likely that the resulting kernel would be as stable and successful as what we have?

그래서 나는 fetchmail에서 성공할 수 있었던 것이 부분적으로는 똑똑해지려는 유혹을 이겨냈기 때문이라고 생각한다. 이것은 (최소한) 성공적인 시장 프로젝트에 설계상의 독창성이 필수적이라는 점에 대해서는 반대되는 주장이다. 리눅스를 생각해 보자. 리누스 토발즈가 개발도중에 운영체에 설계에 있어서 근본적인 혁신을 이끌어내려고 노력했다고 가정해 보자. 결과로 만들어진 커널이 우리에게 있는 것처럼 안정적이고 성공적이었을 것이라고 생각이나 할 수 있을까?

A certain base level of design and coding skill is required, of course, but I expect almost anybody seriously thinking of launching a bazaar effort will already be above that minimum. The open-source community's internal market in reputation exerts subtle pressure on people not to launch development efforts they're not competent to follow through on. So far this seems to have worked pretty well.

어느 정도 기본적인 수준의 설계와 코딩기술은 물론 필요하긴 하지만 시장 스타일의 프로젝트를 시작하려고 심각하게 생각하고 있는 사람이라면 그런 정도는 최소한 넘어섰으리라고 기대하는 것이다. 평판에 대한 오픈 소스 공동체의 내부시장은 미묘한 압력을 사람들에게 작용한다. 그래서 지속적으로 따라갈 수 있는 경쟁력을 가지고 있지 않은 사람이라면 개발 프로젝트를 시작하지 않게 된다. 지금까지 이것은 잘 들어맞아왔던 것 같다.

There is another kind of skill not normally associated with software development which I think is as important as design cleverness to bazaar projects—and it may be more important. A bazaar project coordinator or leader must have good people and communications skills.

시장 스타일의 프로젝트에서 똑똑한 설계만큼이나 중요하다고 생각하는 것이지만 일반적으로 소프트웨어 개발과는 연관짓지 않는 또 한 종류의 기술이 있다 -- 어쩌면 더 중요할 지도 모른다. 시장스타일의 프로젝트를 조정하거나 이끄는 사람은 사람들과 잘 의사소통하는 기술을 가지고 있어야 한다.

This should be obvious. In order to build a development community, you need to attract people, interest them in what you're doing, and keep them happy about the amount of work they're doing. Technical sizzle will go a long way towards accomplishing this, but it's far from the whole story. The personality you project matters, too.

이것은 명확하다. 개발자들의 공동체를 만들려면 사람들을 끌어모아야 하고 무엇을 하고 있는지 그들에게 흥미를 주어야 하고 그들이 하는 일의 결과에 대해서 기분좋을 수 있도록 만들어 주어야 한다. 기술적인 논란(sizzle)은 이런 것을 이룩하는데 도움이 많이 되긴 하지만 그것이 전부는 아니다. 그 사람의 성격도 크게 작용을 한다.

It is not a coincidence that Linus is a nice guy who makes people like him and want to help him. It's not a coincidence that I'm an energetic extrovert who enjoys working a crowd and has some of the delivery and instincts of a stand-up comic. To make the bazaar model work, it helps enormously if you have at least a little skill at charming people.

리누스가 괜찮은 녀석이고 다른 사람들이 그를 좋아하게 되며 그를 도와주고 싶어한다는 것은 우연이 아니다. 내가 정력적이고 외향적이며 많은 사람들과 일하는 것을 즐기고 만화속의 인물 비슷한 인상을 주는 것은 우연이 아니다. 시장 모델이 성공하게 하려면 자신이 사람을 끄는 매력이 조금이라도 있는 것이 매우 큰 도움이 된다.

## The Social Context of Open-Source Software

## 오픈 소스 소프트웨어의 사회적 문맥

It is truly written: the best hacks start out as personal solutions to the author's everyday problems, and spread because the problem turns out to be typical for a large class of users. This takes us back to the matter of rule 1, restated in a perhaps more useful way:

다음과 같은 말이 있다. 가장 뛰어난 해킹은 해커의 일상적인 문제를 푸는 개인적인 해결책으로 시작한다. 그리고 그 문제가 많은 사용자들에게 전형적이라는 것이 밝혀지면 널리 퍼지게 된다. 첫번째 법칙으로 되돌아와 (아마도) 더 유용한 방식으로 다시 말해보자.

18. 18. To solve an interesting problem, start by finding a problem that is interesting to you.

18. 재미있는 문제를 풀어보고 싶다면 자신에게 재미있는 문제를 찾아 나서는 것부터 시작하라. (To solve an interesting problem, start by finding a problem that is interesting to you)

So it was with Carl Harris and the ancestral popclient, and so with me and fetchmail. But this has been understood for a long time. The interesting point, the point that the histories of Linux and fetchmail seem to demand we focus on, is the next stage—the evolution of software in the presence of a large and active community of users and co-developers.

칼 해리스와 popclient 가 그러했고, 나와 fetchmail 이 그러했다. 하지만 이것은 오래전부터 이해되고 있었다. 재미있는 점은 리눅스와 fetchmail 의 역사가 가리키고 있는 것처럼 다음 단계에 있다 -- 사용자와 공동개발자가 이루는 크고 활동적인 공동체의 눈앞에서 소프트웨어가 진화해 가는 것이다.

In The Mythical Man-Month, Fred Brooks observed that programmer time is not fungible; adding developers to a late software project makes it later. As we've seen previously, he argued that the complexity and communication costs of a project rise with the square of the number of developers, while work done only rises linearly. Brooks's Law has been widely regarded as a truism. But we've examined in this essay an number of ways in which the process of open-source development falsifies the assumptionms behind it—and, empirically, if Brooks's Law were the whole picture Linux would be impossible.

``Man-Month 의 신화'' 에서 프레드 브룩스는 프로그래머의 시간이 다른 것으로 대체될 수 없다고 진술했다. 지연되고 있는 소프트웨어 프로젝트에 개발자를 더 투입하는 것은 완료 시기를 더 늦출 뿐이다. 그는 프로젝트에서 복잡성과 의사소통에 드는 비용이 개발자의 제곱에 비례하는 반면 일이 되어가는 것은 직선적으로만 증가한다고 주장했다. 이 주장은 그때부터 ``브룩스의 법칙'' 으로 알려졌고 널리 자명한 이치로 간주되었다. 하지만 브룩스의 법칙이 전부라면 리눅스는 불가능했을 것이다.

Gerald Weinberg's classic The Psychology of Computer Programming supplied what, in hindsight, we can see as a vital correction to Brooks. In his discussion of ``egoless programming'', Weinberg observed that in shops where developers are not territorial about their code, and encourage other people to look for bugs and potential improvements in it, improvement happens dramatically faster than elsewhere. (Recently, Kent Beck's `extreme programming' technique of deploying coders in pairs looking over one anothers' shoulders might be seen as an attempt to force this effect.)

나중에 제랄드 와인버그의 고전인 ``컴퓨터 프로그래밍의 심리학 (The Psychology Of Computer Programming)'' 에서 브룩스의 말에 대한 중요한 수정사항이 제시되었다는 것을 알 수 있다. ``자아를 내세우지 않는 프로그래밍(egoless programming)'' 에 대한 논의에서 와인버그는 개발자들이 자신의 코드에 대해서 텃세를 부리지 않고 다른 사람들로 하여금 버그를 찾고 개선가능성을 찾아내도록 격려하는 곳에서는 다른 어느 곳에서보다 극적으로 빠른 개선이 일어난다고 이야기했다.

Weinberg's choice of terminology has perhaps prevented his analysis from gaining the acceptance it deserved—one has to smile at the thought of describing Internet hackers as ``egoless''. But I think his argument looks more compelling today than ever.

아마도 와인버그의 분석이 적절한 평가를 받지 못했던 것은 용어선택의 문제 때문이었을 것이다 -- 인터넷의 해커들이 ``자아를 내세우지 않는다'' 고 묘사하는 것에는 웃음을 지을 수밖에 없다. 하지만 나는 그의 주장이 지금 그 어느때보다 절실하다고 생각한다.

The bazaar method, by harnessing the full power of the ``egoless programming'' effect, strongly mitigates the effect of Brooks's Law. The principle behind Brooks's Law is not repealed, but given a large developer population and cheap communications its effects can be swamped by competing nonlinearities that are not otherwise visible. This resembles the relationship between Newtonian and Einsteinian physics—the older system is still valid at low energies, but if you push mass and velocity high enough you get surprises like nuclear explosions or Linux.

The history of Unix should have prepared us for what we're learning from Linux (and what I've verified experimentally on a smaller scale by deliberately copying Linus's methods [EGCS]). That is, while coding remains an essentially solitary activity, the really great hacks come from harnessing the attention and brainpower of entire communities. The developer who uses only his or her own brain in a closed project is going to fall behind the developer who knows how to create an open, evolutionary context in which feedback exploring the design space, code contributions, bug-spotting, and other improvements come from from hundreds (perhaps thousands) of people.

유닉스의 역사는 우리가 리눅스로부터 배우고 있는 것을 (그리고 내가 실험적으로 더 작은 스케일로 리누스의 방법을 따라 함으로써 검증한 것을) 미리 준비해 두었어야 했다. 다시 말해 코딩은 본질적으로 고독한 작업인데 비해 정말 중요한 해킹은 전체 공동체의 주의와 지력(brainpower)를 이용함으로써 이루어진다는 것이다. 폐쇄된 프로젝트에서 자신의 두뇌만을 사용하는 개발자는 수백명의 사람들이 버그를 찾아내고 개선을 이루어내는 열려있는 진화적 컨텍스트를 어떻게 만들어내는지 아는 개발자에게 뒤떨어지기 마련이다.

But the traditional Unix world was prevented from pushing this approach to the ultimate by several factors. One was the legal contraints of various licenses, trade secrets, and commercial interests. Another (in hindsight) was that the Internet wasn't yet good enough.

하지만 전통적인 유닉스 세계에는 이런 접근방법을 끝까지 밀어붙이지 못하도록 하는 요인이 몇 가지 있었다. 첫 번째는 다양한 라이센스의 법적인 제약, 거래 비밀, 그리고 상업적인 이해관계였다. 또 하나는 (나중에서야 알게 되었지만) 인터넷이 그리 훌륭하지 못했기 때문이다.

Before cheap Internet, there were some geographically compact communities where the culture encouraged Weinberg's ``egoless'' programming, and a developer could easily attract a lot of skilled kibitzers and co-developers. Bell Labs, the MIT AI and LCS labs, UC Berkeley—these became the home of innovations that are legendary and still potent.

싼 가격에 인터넷을 이용할 수 있게 되기 전에는 지리적으로 좁은 지역에서 공동체가 자리잡고 있었고, 그 공동체의 문화는 와인버그의 ``자아를 내세우지 않는'' 프로그래밍이 장려되었으며 개발자들은 쉽게 많은 사람들, 숙련된 훈수꾼들과 공동개발자들을 끌어들일 수 있었다. 벨 연구소, MIT 인공지능 연구소, UC 버클리 -- 이곳이 바로 전설적인 혁신들이 일어난 곳이고, 여전히 그런 잠재력을 가지고 있는 곳이다.

Linux was the first project for which a conscious and successful effort to use the entire world as its talent pool was made. I don't think it's a coincidence that the gestation period of Linux coincided with the birth of the World Wide Web, and that Linux left its infancy during the same period in 1993–1994 that saw the takeoff of the ISP industry and the explosion of mainstream interest in the Internet. Linus was the first person who learned how to play by the new rules that pervasive Internet access made possible.

리눅스는 재능을 끌어올 풀(pool) 로 전세계를 사용하기 위해 의식적으로, 또 성공적으로 노력한 최초의 프로젝트였다. 나는 리눅스의 태동기가 월드와이드 웹의 탄생과 일치하는 것을, 그리고 리눅스가 유아기를 벗어나던 1993-1994년 경에 ISP 산업과 인터넷에 주류의 관심이 폭발하기 시작했던 것을 우연이라고 생각하지 않는다. 리누스는 급속히 보급되는 인터넷을 가능하게 했던 그 규칙에 따라 어떻게 일을 진행해야 하는지 알았던 최초의 사람이다.

While cheap Internet was a necessary condition for the Linux model to evolve, I think it was not by itself a sufficient condition. Another vital factor was the development of a leadership style and set of cooperative customs that could allow developers to attract co-developers and get maximum leverage out of the medium.

But what is this leadership style and what are these customs? They cannot be based on power relationships—and even if they could be, leadership by coercion would not produce the results we see. Weinberg quotes the autobiography of the 19th-century Russian anarchist Pyotr Alexeyvich Kropotkin's Memoirs of a Revolutionist to good effect on this subject:

저렴하게 인터넷을 사용할 수 있었던 것이 리눅스 모델이 진화하는데 필수적인 조건이었긴 하지만 그것이 충분조건이라고 생각하지는 않는다. 또 하나의 중요한 요소는 리더쉽 스타일과 협력하는 관습의 발전인데, 이것이 개발자로 하여금 공동 개발자를 끌어모으고 매체를 최대한으로 활용하게 했던 것이다. 그렇다면 리더쉽 스타일이란 무엇이고 이런 관습이란 어떤 것인가? 권력관계에 기반한 것은 분명 아니다 -- 만일 그런 것에 기반했다면 강제에 의한 지도력은 우리가 보고있는 것과 같은 결과를 내지 못했을 것이다. 와인버그는 19세기 러시아 무정부주의자인 표트르 알렉세이비치 크로포트킨의 자서전, ``한 혁명가의 비망록''으로부터 다음과 같은 구절을 인용하고 있다.

Having been brought up in a serf-owner's family, I entered active life, like all young men of my time, with a great deal of confidence in the necessity of commanding, ordering, scolding, punishing and the like. But when, at an early stage, I had to manage serious enterprises and to deal with [free] men, and when each mistake would lead at once to heavy consequences, I began to appreciate the difference between acting on the principle of command and discipline and acting on the principle of common understanding. The former works admirably in a military parade, but it is worth nothing where real life is concerned, and the aim can be achieved only through the severe effort of many converging wills.

``농노를 소유한 가정에서 컸기 때문에 나는 내 시대의 모든 젊은이들처럼 능동적인 생활을 했다. 명령하고, 지시하고, 꾸중하고, 벌주는 그런 일에 대한 필요성을 크게 확신하고 있었다. 하지만 내가 큰 사업을 경영해야 했을 때는 다른 사람(자유인)들과 거래해야 했고 단 한 번의 실수가 심각한 결과를 가져올 수 있게 되었을 때 명령과 훈육의 원리에 기반해 행동하는 것과 공동이해의 원리에 의해 행동하는 것 사이의 차이점을 높이 평가하기 시작했다. 군대에서라면 전자에 의해 일하는 것이 훨씬 낫겠지만 실생활에서 많은 사람들의 의지를 수렴하여 노력해야만 이룰 수 있는 목표를 겨냥했을 때는 별 가치가 없다.''

The ``severe effort of many converging wills'' is precisely what a project like Linux requires—and the ``principle of command'' is effectively impossible to apply among volunteers in the anarchist's paradise we call the Internet. To operate and compete effectively, hackers who want to lead collaborative projects have to learn how to recruit and energize effective communities of interest in the mode vaguely suggested by Kropotkin's ``principle of understanding''. They must learn to use Linus's Law.[SP]

``많은 사람들의 의지를 수렴하여 노력하는 것'' 이 바로 리눅스와 같은 프로젝트가 요구하는 것이다 -- 그리고 ``명령의 원리'' 는 결과적으로 우리가 인터넷이라고 부르는 무정부주의자들의 천국에 사는 자원봉사자들에게 적용하기 불가능한 것이다. 효과적으로 일하고 경쟁하기 위해 공동 프로젝트를 이끌어보고 싶은 해커들은 크로포트킨의 ``이해의 원리'' 가 어렴풋이 제시하고 있는 방식에 따라 같은 관심을 가진 공동체를 어떻게 효과적으로 끌어모으고 격려할 것인지 배워야 한다.

Earlier I referred to the ``Delphi effect'' as a possible explanation for Linus's Law. But more powerful analogies to adaptive systems in biology and economics also irresistably suggest themselves. The Linux world behaves in many respects like a free market or an ecology, a collection of selfish agents attempting to maximize utility which in the process produces a self-correcting spontaneous order more elaborate and efficient than any amount of central planning could have achieved. Here, then, is the place to seek the ``principle of understanding''.

앞에서 나는 리누스의 법칙을 설명하기 위해 ``델파이 효과''를 언급했다. 하지만 생물학과 경제학에서의 적응계에 비유하는 것이 더 강력한 비유라고 할 수 있다. 리눅스 세계는 많은 점에서 생태계나 자유시장과 같이 행동한다. 일단의 이기적인 에이전트들이 효용을 극대화시키기 위해 애쓰는 과정을 통해 스스로를 수정하는 자율적인 질서를 만들어 내며 이것은 중앙통제가 이룰 수 있는 어떤 결과보다 더 정교하고 효율적이다. 그렇다면 여기에서 ``이해의 원리''를 찾아낼 수 있다.

The ``utility function'' Linux hackers are maximizing is not classically economic, but is the intangible of their own ego satisfaction and reputation among other hackers. (One may call their motivation ``altruistic'', but this ignores the fact that altruism is itself a form of ego satisfaction for the altruist). Voluntary cultures that work this way are not actually uncommon; one other in which I have long participated is science fiction fandom, which unlike hackerdom has long explicitly recognized ``egoboo'' (ego-boosting, or the enhancement of one's reputation among other fans) as the basic drive behind volunteer activity.

리눅스의 해커들이 최대화하려고 하는 ``효용함수'' 는 고전적인 의미에서의 경제적인 것은 아니고 그들 자신의 측정할 수 없는 자아 만족과 다른 해커들 사이의 평판이다. (이런 동기를 ``이타적'' 이라고 말할 지도 모르겠지만 그렇게 말하는 것은 이타주의 그 자체가 이타주의자의 자아를 만족시키는 한 형태라는 사실을 무시하는 것이다) 이런 방식으로 일을 처리하는 자발적인 문화는 사실 그렇게 찾아보기 힘든 것은 아니다. 내가 오랫동안 참여해왔던 또 하나의 문화는 과학소설 팬들의 세계(science fiction fandom) 이다. 해커들의 세계와 다르지 않게 여기에서는 ``자아상승'' (다른 팬들 사이에서 자신의 평판이 높아지는 것) 이 자발적인 활동 뒤의 기본적인 동기라고 분명하게 인식한다.

Linus, by successfully positioning himself as the gatekeeper of a project in which the development is mostly done by others, and nurturing interest in the project until it became self-sustaining, has shown an acute grasp of Kropotkin's ``principle of shared understanding''. This quasi-economic view of the Linux world enables us to see how that understanding is applied.

리누스는 자신을 개발은 대부분 다른 사람들에 의해 이루어지는 프로젝트의 수문장으로 위치시키는데 성공했고, 프로젝트가 스스로 유지할 수 있게 될 때까지 계속해서 흥미거리를 공급해 줌으로써 크로포트킨의 ``공유이해의 원리''의 의미를 정확하게 따랐다. 준-경제학적인 관점에서 리눅스 세계를 보면 어떻게 이해가 적용되었는지를 알 수 있다.

We may view Linus's method as a way to create an efficient market in ``egoboo''—to connect the selfishness of individual hackers as firmly as possible to difficult ends that can only be achieved by sustained cooperation. With the fetchmail project I have shown (albeit on a smaller scale) that his methods can be duplicated with good results. Perhaps I have even done it a bit more consciously and systematically than he.

리누스의 방법을 ``자아 상승'' 에 있어서 효과적인 시장을 만드는 길로 볼 수 있다 -- 개개인인 해커들의 이기심을 최대한 단단하게 지속적인 협동으로만 이룩할 수 있는 어려운 목적과 연결시키는 것이다. fetchmail 프로젝트에서 나는 (더 작은 규모였지만) 이 방법을 그대로 따라했고 좋은 결과를 냈다. 아마도 내가 리누스보다 더 의식적이고 체계적으로 일을 해냈을 것이다.

Many people (especially those who politically distrust free markets) would expect a culture of self-directed egoists to be fragmented, territorial, wasteful, secretive, and hostile. But this expectation is clearly falsified by (to give just one example) the stunning variety, quality, and depth of Linux documentation. It is a hallowed given that programmers hate documenting; how is it, then, that Linux hackers generate so much documentation? Evidently Linux's free market in egoboo works better to produce virtuous, other-directed behavior than the massively-funded documentation shops of commercial software producers.

많은 사람들이 (특히 자유시장을 정치적으로 믿지 않는 사람들) 스스로에게 방향이 맞추어진 이기주의자들의 문화가 파편화되어 있으며 텃세가 심하고 소모적이면서, 비밀이 많고 적대적일 것이라고 생각한다. 하지만 이런 기대는 (예를 하나만 들자면) 리눅스 문서의 놀랄만한 다양성과 질, 깊이에 의해 산산이 부서지고 만다. 프로그래머들이 문서작업을 끔직하게 싫어한다는 것은 모두가 기정사실로 받아들이고 있다. 그렇다면 대체, 리눅스 해커들이 문서를 그렇게 많이 만들어냈다는 것은 어떻게 설명할 것인가? 분명히 리눅스의 자아상승을 위한 자유시장은 막대한 자금이 들어간 상업용 소프트웨어 프로듀서들의 문서작업보다 다른 사람을 위한 고결한 행동을 더 잘 해낸 것이다.

Both the fetchmail and Linux kernel projects show that by properly rewarding the egos of many other hackers, a strong developer/coordinator can use the Internet to capture the benefits of having lots of co-developers without having a project collapse into a chaotic mess. So to Brooks's Law I counter-propose the following:

fetchmail 과 리눅스 커널 프로젝트는 둘 다 많은 해커들의 자아를 적절히 보상해 줌으로써 강력한 개발자/조정자가 인터넷을 이용하여 많은 수의 공동개발자를 가지는 이익을 얻으면서 프로젝트가 혼돈스럽게 스스로 붕괴하는 것을 막을 수 있다는 것을 보여준다. 브룩스의 법칙에 대해서 나는 다음과 같은 반대제안을 한다.

19: Provided the development coordinator has a communications medium at least as good as the Internet, and knows how to lead without coercion, many heads are inevitably better than one.

19. 개발 조정자가 최소한 인터넷만큼 좋은 매체를 가지고 있으며 강제력을 사용하지 않고 어떻게 이끌어야 할 지 알고 있다면 한 명 보다는 여러명의 리더가 필연적으로 더 낫다. (Provided the development coordinator has a medium at least as good as the Internet, and know how to lead without coercion, many heads are inevitable better than one)

I think the future of open-source software will increasingly belong to people who know how to play Linus's game, people who leave behind the cathedral and embrace the bazaar. This is not to say that individual vision and brilliance will no longer matter; rather, I think that the cutting edge of open-source software will belong to people who start from individual vision and brilliance, then amplify it through the effective construction of voluntary communities of interest.

미래의 오픈 소스 소프트웨어는 점점 리누스의 게임을 어떻게 해야 하는지 아는 사람들, 성당을 뒤로 하고 시장을 끌어안을 수 있는 사람들에게 속할 것이라고 생각한다. 개인의 비전과 똑똑함이 문제가 되지 않으리라는 말이라기보다는 오픈 소스 소프트웨어의 최첨단은 개인의 비전과 똑똑함에서 시작하여 자발적으로 흥미를 보이는 공동체를 효과적으로 구축해서 그것을 증폭시키는 사람들에게 속할 것이라는 뜻이다.

Perhaps this is not only the future of open-source software. No closed-source developer can match the pool of talent the Linux community can bring to bear on a problem. Very few could afford even to hire the more than 200 (1999: 600, 2000: 800) people who have contributed to fetchmail!

그리고 그것은 오픈소스 소프트웨어의 미래에만 국한되지는 않을 것이다. 닫혀있는 소스로 개발하는 사람은 리눅스 공동체가 문제를 해결하기 위해 끌어낼 수 있는 재능의 풀과 경쟁할 수 없다. 극소수만이 fetchmail 에 공헌했던 200명보다 많은 사람을 고용할 수 있을 것이다. 

Perhaps in the end the open-source culture will triumph not because cooperation is morally right or software ``hoarding'' is morally wrong (assuming you believe the latter, which neither Linus nor I do), but simply because the closed-source world cannot win an evolutionary arms race with open-source communities that can put orders of magnitude more skilled time into a problem.

아마 최종적으로는 협동이 더 도덕적이라거나 소프트웨어 ``매점'' 이 덜 도덕적이라서가 아니라 단지 닫혀진 소스 측과 오픈 소스 공동체와의 군비경쟁에서 오픈 소스 측이 한 문제에 훨씬 큰 비율로 숙련된 사람의 시간을 쏟을 수 있기 때문에 오픈 소스 문화가 승리를 거둘 것이라는 얘기다.

## On Management and the Maginot Line

The original Cathedral and Bazaar paper of 1997 ended with the vision above—that of happy networked hordes of programmer/anarchists outcompeting and overwhelming the hierarchical world of conventional closed software.

A good many skeptics weren't convinced, however; and the questions they raise deserve a fair engagement. Most of the objections to the bazaar argument come down to the claim that its proponents have underestimated the productivity-multiplying effect of conventional management.

Traditionally-minded software-development managers often object that the casualness with which project groups form and change and dissolve in the open-source world negates a significant part of the apparent advantage of numbers that the open-source community has over any single closed-source developer. They would observe that in software development it is really sustained effort over time and the degree to which customers can expect continuing investment in the product that matters, not just how many people have thrown a bone in the pot and left it to simmer.

There is something to this argument, to be sure; in fact, I have developed the idea that expected future service value is the key to the economics of software production in the essay The Magic Cauldron.

But this argument also has a major hidden problem; its implicit assumption that open-source development cannot deliver such sustained effort. In fact, there have been open-source projects that maintained a coherent direction and an effective maintainer community over quite long periods of time without the kinds of incentive structures or institutional controls that conventional management finds essential. The development of the GNU Emacs editor is an extreme and instructive example; it has absorbed the efforts of hundreds of contributors over 15 years into a unified architectural vision, despite high turnover and the fact that only one person (its author) has been continuously active during all that time. No closed-source editor has ever matched this longevity record.

This suggests a reason for questioning the advantages of conventionally-managed software development that is independent of the rest of the arguments over cathedral vs. bazaar mode. If it's possible for GNU Emacs to express a consistent architectural vision over 15 years, or for an operating system like Linux to do the same over 8 years of rapidly changing hardware and platform technology; and if (as is indeed the case) there have been many well-architected open-source projects of more than 5 years duration -- then we are entitled to wonder what, if anything, the tremendous overhead of conventionally-managed development is actually buying us.

Whatever it is certainly doesn't include reliable execution by deadline, or on budget, or to all features of the specification; it's a rare `managed' project that meets even one of these goals, let alone all three. It also does not appear to be ability to adapt to changes in technology and economic context during the project lifetime, either; the open-source community has proven far more effective on that score (as one can readily verify, for example, by comparing the 30-year history of the Internet with the short half-lives of proprietary networking technologies—or the cost of the 16-bit to 32-bit transition in Microsoft Windows with the nearly effortless upward migration of Linux during the same period, not only along the Intel line of development but to more than a dozen other hardware platforms, including the 64-bit Alpha as well).

One thing many people think the traditional mode buys you is somebody to hold legally liable and potentially recover compensation from if the project goes wrong. But this is an illusion; most software licenses are written to disclaim even warranty of merchantability, let alone performance—and cases of successful recovery for software nonperformance are vanishingly rare. Even if they were common, feeling comforted by having somebody to sue would be missing the point. You didn't want to be in a lawsuit; you wanted working software.

So what is all that management overhead buying?

In order to understand that, we need to understand what software development managers believe they do. A woman I know who seems to be very good at this job says software project management has five functions:

* To define goals and keep everybody pointed in the same direction
* To monitor and make sure crucial details don't get skipped
* To motivate people to do boring but necessary drudgework
* To organize the deployment of people for best productivity
* To marshal resources needed to sustain the project

Apparently worthy goals, all of these; but under the open-source model, and in its surrounding social context, they can begin to seem strangely irrelevant. We'll take them in reverse order.

My friend reports that a lot of resource marshalling is basically defensive; once you have your people and machines and office space, you have to defend them from peer managers competing for the same resources, and from higher-ups trying to allocate the most efficient use of a limited pool.

But open-source developers are volunteers, self-selected for both interest and ability to contribute to the projects they work on (and this remains generally true even when they are being paid a salary to hack open source.) The volunteer ethos tends to take care of the `attack' side of resource-marshalling automatically; people bring their own resources to the table. And there is little or no need for a manager to `play defense' in the conventional sense.

Anyway, in a world of cheap PCs and fast Internet links, we find pretty consistently that the only really limiting resource is skilled attention. Open-source projects, when they founder, essentially never do so for want of machines or links or office space; they die only when the developers themselves lose interest.

That being the case, it's doubly important that open-source hackers organize themselves for maximum productivity by self-selection—and the social milieu selects ruthlessly for competence. My friend, familiar with both the open-source world and large closed projects, believes that open source has been successful partly because its culture only accepts the most talented 5% or so of the programming population. She spends most of her time organizing the deployment of the other 95%, and has thus observed first-hand the well-known variance of a factor of one hundred in productivity between the most able programmers and the merely competent.

The size of that variance has always raised an awkward question: would individual projects, and the field as a whole, be better off without more than 50% of the least able in it? Thoughtful managers have understood for a long time that if conventional software management's only function were to convert the least able from a net loss to a marginal win, the game might not be worth the candle.

The success of the open-source community sharpens this question considerably, by providing hard evidence that it is often cheaper and more effective to recruit self-selected volunteers from the Internet than it is to manage buildings full of people who would rather be doing something else.

Which brings us neatly to the question of motivation. An equivalent and often-heard way to state my friend's point is that traditional development management is a necessary compensation for poorly motivated programmers who would not otherwise turn out good work.

This answer usually travels with a claim that the open-source community can only be relied on only to do work that is `sexy' or technically sweet; anything else will be left undone (or done only poorly) unless it's churned out by money-motivated cubicle peons with managers cracking whips over them. I address the psychological and social reasons for being skeptical of this claim in Homesteading the Noosphere. For present purposes, however, I think it's more interesting to point out the implications of accepting it as true.

If the conventional, closed-source, heavily-managed style of software development is really defended only by a sort of Maginot Line of problems conducive to boredom, then it's going to remain viable in each individual application area for only so long as nobody finds those problems really interesting and nobody else finds any way to route around them. Because the moment there is open-source competition for a `boring' piece of software, customers are going to know that it was finally tackled by someone who chose that problem to solve because of a fascination with the problem itself—which, in software as in other kinds of creative work, is a far more effective motivator than money alone.

Having a conventional management structure solely in order to motivate, then, is probably good tactics but bad strategy; a short-term win, but in the longer term a surer loss.

So far, conventional development management looks like a bad bet now against open source on two points (resource marshalling, organization), and like it's living on borrowed time with respect to a third (motivation). And the poor beleaguered conventional manager is not going to get any succour from the monitoring issue; the strongest argument the open-source community has is that decentralized peer review trumps all the conventional methods for trying to ensure that details don't get slipped.

Can we save defining goals as a justification for the overhead of conventional software project management? Perhaps; but to do so, we'll need good reason to believe that management committees and corporate roadmaps are more successful at defining worthy and widely shared goals than the project leaders and tribal elders who fill the analogous role in the open-source world.

That is on the face of it a pretty hard case to make. And it's not so much the open-source side of the balance (the longevity of Emacs, or Linus Torvalds's ability to mobilize hordes of developers with talk of ``world domination'') that makes it tough. Rather, it's the demonstrated awfulness of conventional mechanisms for defining the goals of software projects.

One of the best-known folk theorems of software engineering is that 60% to 75% of conventional software projects either are never completed or are rejected by their intended users. If that range is anywhere near true (and I've never met a manager of any experience who disputes it) then more projects than not are being aimed at goals that are either (a) not realistically attainable, or (b) just plain wrong.

This, more than any other problem, is the reason that in today's software engineering world the very phrase ``management committee'' is likely to send chills down the hearer's spine—even (or perhaps especially) if the hearer is a manager. The days when only programmers griped about this pattern are long past; Dilbert cartoons hang over executives' desks now.

Our reply, then, to the traditional software development manager, is simple—if the open-source community has really underestimated the value of conventional management, why do so many of you display contempt for your own process?

Once again the example of the open-source community sharpens this question considerably—because we have fun doing what we do. Our creative play has been racking up technical, market-share, and mind-share successes at an astounding rate. We're proving not only that we can do better software, but that joy is an asset.

Two and a half years after the first version of this essay, the most radical thought I can offer to close with is no longer a vision of an open-source–dominated software world; that, after all, looks plausible to a lot of sober people in suits these days.

Rather, I want to suggest what may be a wider lesson about software, (and probably about every kind of creative or professional work). Human beings generally take pleasure in a task when it falls in a sort of optimal-challenge zone; not so easy as to be boring, not too hard to achieve. A happy programmer is one who is neither underutilized nor weighed down with ill-formulated goals and stressful process friction. Enjoyment predicts efficiency.

Relating to your own work process with fear and loathing (even in the displaced, ironic way suggested by hanging up Dilbert cartoons) should therefore be regarded in itself as a sign that the process has failed. Joy, humor, and playfulness are indeed assets; it was not mainly for the alliteration that I wrote of "happy hordes" above, and it is no mere joke that the Linux mascot is a cuddly, neotenous penguin.

It may well turn out that one of the most important effects of open source's success will be to teach us that play is the most economically efficient mode of creative work.


## Epilog: Netscape Embraces the Bazaar
## 후기 : 넷스케이프가 시장 스타일을 받아들이다!

It's a strange feeling to realize you're helping make history....

역사가 만들어지는 데 일조를 했다는 사실을 깨닫는 것은 좀 이상한 느낌이다.

On January 22 1998, approximately seven months after I first published The Cathedral and the Bazaar, Netscape Communications, Inc. announced plans to give away the source for Netscape Communicator. I had had no clue this was going to happen before the day of the announcement.

1998년 1월 22일, 내가 처음으로 이 글을 발표한 지 7달 정도 지난 시점에서 넷스케이프 사가 넷스케이프 커뮤니케이터의 소스를 공개하기로 했다고 발표했다.

Eric Hahn, executive vice president and chief technology officer at Netscape, emailed me shortly afterwards as follows: ``On behalf of everyone at Netscape, I want to thank you for helping us get to this point in the first place. Your thinking and writings were fundamental inspirations to our decision.''

나는 발표가 있기 전날 까지도 이런 일이 일어나리라고는 생각하지 못했다. 넷스케이프의 수석 기술담당이자 부사장인 에릭 한(Eric Hahn) 은 발표직후에 다음과 같은 email을 보냈다. ``넷스케이프의 모든 사람들을 대신해 우리가 이곳까지 오도록 도와주신 것에 감사드립니다. 당신의 생각과 글이 우리의 결정에 근본적인 영감을 주었습니다.''

The following week I flew out to Silicon Valley at Netscape's invitation for a day-long strategy conference (on 4 Feb 1998) with some of their top executives and technical people. We designed Netscape's source-release strategy and license together.

A few days later I wrote the following:

Netscape is about to provide us with a large-scale, real-world test of the bazaar model in the commercial world. The open-source culture now faces a danger; if Netscape's execution doesn't work, the open-source concept may be so discredited that the commercial world won't touch it again for another decade.

On the other hand, this is also a spectacular opportunity. Initial reaction to the move on Wall Street and elsewhere has been cautiously positive. We're being given a chance to prove ourselves, too. If Netscape regains substantial market share through this move, it just may set off a long-overdue revolution in the software industry.

The next year should be a very instructive and interesting time.

And indeed it was. As I write in mid-2000, the development of what was later named Mozilla has been only a qualified success. It achieved Netscape's original goal, which was to deny Microsoft a monopoly lock on the browser market. It has also achieved some dramatic successes (notably the release of the next-generation Gecko rendering engine).

However, it has not yet garnered the massive development effort from outside Netscape that the Mozilla founders had originally hoped for. The problem here seems to be that for a long time the Mozilla distribution actually broke one of the basic rules of the bazaar model; it didn't ship with something potential contributors could easily run and see working. (Until more than a year after release, building Mozilla from source required a license for the proprietary Motif library.)

Most negatively (from the point of view of the outside world) the Mozilla group didn't ship a production-quality browser for two and a half years after the project launch—and in 1999 one of the project's principals caused a bit of a sensation by resigning, complaining of poor management and missed opportunities. ``Open source,'' he correctly observed, ``is not magic pixie dust.''

And indeed it is not. The long-term prognosis for Mozilla looks dramatically better now (in November 2000) than it did at the time of Jamie Zawinski's resignation letter—in the last few weeks the nightly releases have finally passed the critical threshold to production usability. But Jamie was right to point out that going open will not necessarily save an existing project that suffers from ill-defined goals or spaghetti code or any of the software engineering's other chronic ills. Mozilla has managed to provide an example simultaneously of how open source can succeed and how it could fail.

In the mean time, however, the open-source idea has scored successes and found backers elsewhere. Since the Netscape release we've seen a tremendous explosion of interest in the open-source development model, a trend both driven by and driving the continuing success of the Linux operating system. The trend Mozilla touched off is continuing at an accelerating rate.

그 다음주에 나는 넷스케이프 사의 초청으로 실리콘 밸리에 가서 고위 경영진 및 기술진들과 함께 하루짜리 전략회의(1998년 2월 4일)에 참석했다. 우리는 넷스케이프의 소스 공개 전략과 라이센스를 함께 설계했고 최종적으로는 오픈 소스 공동체에 크고 긍정적인 영향을 끼칠 것으로 희망하는 몇몇 계획을 만들었다. 이 글을 쓰는 시점에서 더 자세히 언급하기에는 너무 이르지만 수주일 내에 자세한 사항이 발표될 것이다.

넷스케이프는 시장모델을 상업계에서 대규모로 실제 테스트할 수 있는 기회를 제공하려 한다. 오픈 소스 문화는 이제 위험을 맞이하게 된 것이다. 넷스케이프의 시도가 실패한다면 오픈 소스 개념은 불신을 받을 것이고 상업계에서 향후 십년간은 오픈 소스를 다시 받아들이려 하지 않을 것이다.

반면에 볼만한 기회가 될 수도 있다. 월스트리트 등의 첫 반응은 조심스럽지만 긍정적이었다. 우리 자신을 증명할 기회를 얻은 것이다. 만일 넷스케이프가 이번 행보로 상당한 양의 시장점유율을 끌어올린다면 컴퓨터 산업에서 오래 전에 이루어졌어야 했던 혁명을 시작하게 되는 것이다. 다음 한 해는 매우 교육적이며 재미있는 한 해가 될 것이다.

## Notes

[JB] In Programing Pearls, the noted computer-science aphorist Jon Bentley comments on Brooks's observation with ``If you plan to throw one away, you will throw away two.''. He is almost certainly right. The point of Brooks's observation, and Bentley's, isn't merely that you should expect first attempt to be wrong, it's that starting over with the right idea is usually more effective than trying to salvage a mess.

[QR] Examples of successful open-source, bazaar development predating the Internet explosion and unrelated to the Unix and Internet traditions have existed. The development of the info-Zip compression utility during 1990–x1992, primarily for DOS machines, was one such example. Another was the RBBS bulletin board system (again for DOS), which began in 1983 and developed a sufficiently strong community that there have been fairly regular releases up to the present (mid-1999) despite the huge technical advantages of Internet mail and file-sharing over local BBSs. While the info-Zip community relied to some extent on Internet mail, the RBBS developer culture was actually able to base a substantial on-line community on RBBS that was completely independent of the TCP/IP infrastructure.

[CV] That transparency and peer review are valuable for taming the complexity of OS development turns out, after all, not to be a new concept. In 1965, very early in the history of time-sharing operating systems, Corbató and Vyssotsky, co-designers of the Multics operating system, wrote

It is expected that the Multics system will be published when it is operating substantially... Such publication is desirable for two reasons: First, the system should withstand public scrutiny and criticism volunteered by interested readers; second, in an age of increasing complexity, it is an obligation to present and future system designers to make the inner operating system as lucid as possible so as to reveal the basic system issues.

[JH] John Hasler has suggested an interesting explanation for the fact that duplication of effort doesn't seem to be a net drag on open-source development. He proposes what I'll dub ``Hasler's Law'': the costs of duplicated work tend to scale sub-qadratically with team size—that is, more slowly than the planning and management overhead that would be needed to eliminate them.

This claim actually does not contradict Brooks's Law. It may be the case that total complexity overhead and vulnerability to bugs scales with the square of team size, but that the costs from duplicated work are nevertheless a special case that scales more slowly. It's not hard to develop plausible reasons for this, starting with the undoubted fact that it is much easier to agree on functional boundaries between different developers' code that will prevent duplication of effort than it is to prevent the kinds of unplanned bad interactions across the whole system that underly most bugs.

The combination of Linus's Law and Hasler's Law suggests that there are actually three critical size regimes in software projects. On small projects (I would say one to at most three developers) no management structure more elaborate than picking a lead programmer is needed. And there is some intermediate range above that in which the cost of traditional management is relatively low, so its benefits from avoiding duplication of effort, bug-tracking, and pushing to see that details are not overlooked actually net out positive.

Above that, however, the combination of Linus's Law and Hasler's Law suggests there is a large-project range in which the costs and problems of traditional management rise much faster than the expected cost from duplication of effort. Not the least of these costs is a structural inability to harness the many-eyeballs effect, which (as we've seen) seems to do a much better job than traditional management at making sure bugs and details are not overlooked. Thus, in the large-project case, the combination of these laws effectively drives the net payoff of traditional management to zero.

[HBS] The split between Linux's experimental and stable versions has another function related to, but distinct from, hedging risk. The split attacks another problem: the deadliness of deadlines. When programmers are held both to an immutable feature list and a fixed drop-dead date, quality goes out the window and there is likely a colossal mess in the making. I am indebted to Marco Iansiti and Alan MacCormack of the Harvard Business School for showing me me evidence that relaxing either one of these constraints can make scheduling workable.

One way to do this is to fix the deadline but leave the feature list flexible, allowing features to drop off if not completed by deadline. This is essentially the strategy of the "stable" kernel branch; Alan Cox (the stable-kernel maintainer) puts out releases at fairly regular intervals, but makes no guarantees about when particular bugs will be fixed or what features will beback-ported from the experimental branch.

The other way to do this is to set a desired feature list and deliver only when it is done. This is essentially the strategy of the "experimental" kernel branch. De Marco and Lister cited research showing that this scheduling policy ("wake me up when it's done") produces not only the highest quality but, on average, shorter delivery times than either "realistic" or "aggressive" scheduling.

I have come to suspect (as of early 2000) that in earlier versions of this essay I severely underestimated the importance of the "wake me up when it's done" anti-deadline policy to the open-source community's productivity and quality. General experience with the rushed GNOME 1.0 release in 1999 suggests that pressure for a premature release can neutralize many of the quality benefits open source normally confers.

It may well turn out to be that the process transparency of open source is one of three co-equal drivers of its quality, along with "wake me up when it's done" scheduling and developer self-selection.

[SU] It's tempting, and not entirely inaccurate, to see the core-plus-halo organization characteristic of open-source projects as an Internet-enabled spin on Brooks's own recommendation for solving the N-squared complexity problem, the "surgical-team" organization—but the differences are significant. The constellation of specialist roles such as "code librarian" that Brooks envisioned around the team leader doesn't really exist; those roles are executed instead by generalists aided by toolsets quite a bit more powerful than those of Brooks's day. Also, the open-source culture leans heavily on strong Unix traditions of modularity, APIs, and information hiding—none of which were elements of Brooks's prescription.

[RJ] The respondent who pointed out to me the effect of widely varying trace path lengths on the difficulty of characterizing a bug speculated that trace-path difficulty for multiple symptoms of the same bug varies "exponentially" (which I take to mean on a Gaussian or Poisson distribution, and agree seems very plausible). If it is experimentally possible to get a handle on the shape of this distribution, that would be extremely valuable data. Large departures from a flat equal-probability distribution of trace difficulty would suggest that even solo developers should emulate the bazaar strategy by bounding the time they spend on tracing a given symptom before they switch to another. Persistence may not always be a virtue...

[IN] An issue related to whether one can start projects from zero in the bazaar style is whether the bazaar style is capable of supporting truly innovative work. Some claim that, lacking strong leadership, the bazaar can only handle the cloning and improvement of ideas already present at the engineering state of the art, but is unable to push the state of the art. This argument was perhaps most infamously made by the Halloween Documents, two embarrassing internal Microsoft memoranda written about the open-source phenomenon. The authors compared Linux's development of a Unix-like operating system to ``chasing taillights'', and opined ``(once a project has achieved "parity" with the state-of-the-art), the level of management necessary to push towards new frontiers becomes massive.''

There are serious errors of fact implied in this argument. One is exposed when the Halloween authors themseselves later observe that ``often [...] new research ideas are first implemented and available on Linux before they are available / incorporated into other platforms.''

If we read ``open source'' for ``Linux'', we see that this is far from a new phenomenon. Historically, the open-source community did not invent Emacs or the World Wide Web or the Internet itself by chasing taillights or being massively managed—and in the present, there is so much innovative work going on in open source that one is spoiled for choice. The GNOME project (to pick one of many) is pushing the state of the art in GUIs and object technology hard enough to have attracted considerable notice in the computer trade press well outside the Linux community. Other examples are legion, as a visit to Freshmeat on any given day will quickly prove.

But there is a more fundamental error in the implicit assumption that the cathedral model (or the bazaar model, or any other kind of management structure) can somehow make innovation happen reliably. This is nonsense. Gangs don't have breakthrough insights—even volunteer groups of bazaar anarchists are usually incapable of genuine originality, let alone corporate committees of people with a survival stake in some status quo ante. Insight comes from individuals. The most their surrounding social machinery can ever hope to do is to be responsive to breakthrough insights—to nourish and reward and rigorously test them instead of squashing them.

Some will characterize this as a romantic view, a reversion to outmoded lone-inventor stereotypes. Not so; I am not asserting that groups are incapable of developing breakthrough insights once they have been hatched; indeed, we learn from the peer-review process that such development groups are essential to producing a high-quality result. Rather I am pointing out that every such group development starts from—is necessarily sparked by—one good idea in one person's head. Cathedrals and bazaars and other social structures can catch that lightning and refine it, but they cannot make it on demand.

Therefore the root problem of innovation (in software, or anywhere else) is indeed how not to squash it—but, even more fundamentally, it is how to grow lots of people who can have insights in the first place.

To suppose that cathedral-style development could manage this trick but the low entry barriers and process fluidity of the bazaar cannot would be absurd. If what it takes is one person with one good idea, then a social milieu in which one person can rapidly attract the cooperation of hundreds or thousands of others with that good idea is going inevitably to out-innovate any in which the person has to do a political sales job to a hierarchy before he can work on his idea without risk of getting fired.

And, indeed, if we look at the history of software innovation by organizations using the cathedral model, we quickly find it is rather rare. Large corporations rely on university research for new ideas (thus the Halloween Documents authors' unease about Linux's facility at coopting that research more rapidly). Or they buy out small companies built around some innovator's brain. In neither case is the innovation native to the cathedral culture; indeed, many innovations so imported end up being quietly suffocated under the "massive level of management" the Halloween Documents' authors so extol.

That, however, is a negative point. The reader would be better served by a positive one. I suggest, as an experiment, the following:

Pick a criterion for originality that you believe you can apply consistently. If your definition is ``I know it when I see it'', that's not a problem for purposes of this test.

Pick any closed-source operating system competing with Linux, and a best source for accounts of current development work on it.

Watch that source and Freshmeat for one month. Every day, count the number of release announcements on Freshmeat that you consider `original' work. Apply the same definition of `original' to announcements for that other OS and count them.

Thirty days later, total up both figures.

The day I wrote this, Freshmeat carried twenty-two release announcements, of which three appear they might push state of the art in some respect, This was a slow day for Freshmeat, but I will be astonished if any reader reports as many as three likely innovations a month in any closed-source channel.

[EGCS] We now have history on a project that, in several ways, may provide a more indicative test of the bazaar premise than fetchmail; EGCS, the Experimental GNU Compiler System.

This project was announced in mid-August of 1997 as a conscious attempt to apply the ideas in the early public versions of The Cathedral and the Bazaar. The project founders felt that the development of GCC, the Gnu C Compiler, had been stagnating. For about twenty months afterwards, GCC and EGCS continued as parallel products—both drawing from the same Internet developer population, both starting from the same GCC source base, both using pretty much the same Unix toolsets and development environment. The projects differed only in that EGCS consciously tried to apply the bazaar tactics I have previously described, while GCC retained a more cathedral-like organization with a closed developer group and infrequent releases.

This was about as close to a controlled experiment as one could ask for, and the results were dramatic. Within months, the EGCS versions had pulled substantially ahead in features; better optimization, better support for FORTRAN and C++. Many people found the EGCS development snapshots to be more reliable than the most recent stable version of GCC, and major Linux distributions began to switch to EGCS.

In April of 1999, the Free Software Foundation (the official sponsors of GCC) dissolved the original GCC development group and officially handed control of the project to the the EGCS steering team.

[SP] Of course, Kropotkin's critique and Linus's Law raise some wider issues about the cybernetics of social organizations. Another folk theorem of software engineering suggests one of them; Conway's Law—commonly stated as ``If you have four groups working on a compiler, you'll get a 4-pass compiler''. The original statement was more general: ``Organizations which design systems are constrained to produce designs which are copies of the communication structures of these organizations.'' We might put it more succinctly as ``The means determine the ends'', or even ``Process becomes product''.

It is accordingly worth noting that in the open-source community organizational form and function match on many levels. The network is everything and everywhere: not just the Internet, but the people doing the work form a distributed, loosely coupled, peer-to-peer network that provides multiple redundancy and degrades very gracefully. In both networks, each node is important only to the extent that other nodes want to cooperate with it.

The peer-to-peer part is essential to the community's astonishing productivity. The point Kropotkin was trying to make about power relationships is developed further by the `SNAFU Principle': ``True communication is possible only between equals, because inferiors are more consistently rewarded for telling their superiors pleasant lies than for telling the truth.'' Creative teamwork utterly depends on true communication and is thus very seriously hindered by the presence of power relationships. The open-source community, effectively free of such power relationships, is teaching us by contrast how dreadfully much they cost in bugs, in lowered productivity, and in lost opportunities.

Further, the SNAFU principle predicts in authoritarian organizations a progressive disconnect between decision-makers and reality, as more and more of the input to those who decide tends to become pleasant lies. The way this plays out in conventional software development is easy to see; there are strong incentives for the inferiors to hide, ignore, and minimize problems. When this process becomes product, software is a disaster.

## Bibliography

* I quoted several bits from Frederick P. Brooks's classic The Mythical Man-Month because, in many respects, his insights have yet to be improved upon. I heartily recommend the 25th Anniversary edition from Addison-Wesley (ISBN 0-201-83595-9), which adds his 1986 ``No Silver Bullet'' paper.

* The new edition is wrapped up by an invaluable 20-years-later retrospective in which Brooks forthrightly admits to the few judgements in the original text which have not stood the test of time. I first read the retrospective after the first public version of this essay was substantially complete, and was surprised to discover that Brooks attributed bazaar-like practices to Microsoft! (In fact, however, this attribution turned out to be mistaken. In 1998 we learned from the Halloween Documents that Microsoft's internal developer community is heavily balkanized, with the kind of general source access needed to support a bazaar not even truly possible.)

Gerald M. Weinberg's The Psychology Of Computer Programming (New York, Van Nostrand Reinhold 1971) introduced the rather unfortunately-labeled concept of ``egoless programming''. While he was nowhere near the first person to realize the futility of the ``principle of command'', he was probably the first to recognize and argue the point in particular connection with software development.

Richard P. Gabriel, contemplating the Unix culture of the pre-Linux era, reluctantly argued for the superiority of a primitive bazaar-like model in his 1989 paper ``LISP: Good News, Bad News, and How To Win Big''. Though dated in some respects, this essay is still rightly celebrated among LISP fans (including me). A correspondent reminded me that the section titled ``Worse Is Better'' reads almost as an anticipation of Linux. The paper is accessible on the World Wide Web at http://www.naggum.no/worse-is-better.html.

De Marco and Lister's Peopleware: Productive Projects and Teams (New York; Dorset House, 1987; ISBN 0-932633-05-6) is an underappreciated gem which I was delighted to see Fred Brooks cite in his retrospective. While little of what the authors have to say is directly applicable to the Linux or open-source communities, the authors' insight into the conditions necessary for creative work is acute and worthwhile for anyone attempting to import some of the bazaar model's virtues into a commercial context.

Finally, I must admit that I very nearly called this essay ``The Cathedral and the Agora'', the latter term being the Greek for an open market or public meeting place. The seminal ``agoric systems'' papers by Mark Miller and Eric Drexler, by describing the emergent properties of market-like computational ecologies, helped prepare me to think clearly about analogous phenomena in the open-source culture when Linux rubbed my nose in them five years later. These papers are available on the Web at http://www.agorics.com/agorpapers.html.

## 읽어볼 만한 글들

* 프레드릭 브룩스 (Frederick P. Brooks) 의 고전인 [Man-Month 의 신화 (The Mythical Man-Month)]에서 몇몇 부분을 인용했다. 앞으로도 여러 관점에서 그의 통찰력을 발전시킬 수 있을 것이다. 애디슨-웨슬리(Addson-Wesley) 의 25주년 기념판 (ISBN 0-201-83595-9)을 추천한다.여기에는 그가 1986년에 쓴 글, ``은총알은 없다 (No Silver Bullet)''가 들어있다. 새 기념판은 매우 귀중한 20년 후의 회고를 담고 있다. 브룩스는 여기서 원문의 몇몇 판단이 시간이 흐름에 따라 옳지 않은 것으로 드러났다고 솔직하게 인정하고 있다. 나는 이 글을 대략 마무리 지은 후에 회고담을 읽어보았는데, 브룩스가 시장 스타일을 마이크로소프트에서 연유한 관습으로 생각한다는 것을 발견하고 깜짝 놀랐다!

제랄드 M. 와인버그의 [컴퓨터 프로그래밍의 심리학 (The Psychology Of Computer Programming)] (New York, Van Nostrand Reinhold 1971) 은 비운의 개념인 ``자아를 내세우지 않는 프로그래밍'' 을 소개했다. ``명령의 원칙'' 이 무용지물이라는 것을 처음으로 깨달은 사람이 와인버그는 아니지만 그는 아마도 처음으로 그것을 인식하고 특별히 소프트웨어 개발과 관련하여 논지를 전개시킨 첫 번째 사람일 것이다.

리차드 P. 가브리엘 (Richard P. Gabriel) 은 리눅스 이전 시대의 유닉스 문화에 대해 숙고하고 주저하면서도 원시적인 시장 스타일의 모델이 우월하다는 것을 1989년의 글인 [리스프 : 좋은 소식과 나쁜 소식, 그리고 큰 성공을 거두는 방법 (Lisp : Good News, Bad News, and How To Win Big)] 에서 밝혔다. 몇 가지 시대에 뒤떨어진 감은 있지만 이 글은 여전히 리스프의 팬 (나를 포함해서) 들에게 적절한 찬사를 받고 있다. 편지를 교환하던 사람 중 한 명이 나에게 ``나쁜 것이 좋은 것이다 (Worse is Better)'' 라는 제목을 가진 절이 리눅스를 예견하다시피 했다는 것을 상기시켜 주었다. 이 글은 웹에서 다음의 주소에 가면 읽을 수 있다. http://www.naggum.no/worse-is-better.html

드 마르코 (De Marco) 와 리스터 (Lister) 의 [피플웨어: 생산적인 프로젝트와 팀 (Peopleware : Productive Projects and Teams)](New York; Dorset House, 1987; ISBN 0-932633-05-6) 은 결코 평가절하될 수 없는 보석이다. 프레드 브룩스가 회고의 글에서 이 책을 인용해서 기뻤다. 저자가 말하고 있는 것 중에서 리눅스나 오픈 소스 공동체에 바로 적용될 수 있는 것은 거의 없지만 창조적인 작업의 필요조건에 대한 통찰력은 날카롭고 시장 모델의 미덕을 상업적인 문맥에 결합시키려고 하는 사람에게는 가치있는 것이다.

마지막으로 사실 나는 이 글을 ``성당과 광장 (The Cathedral and the Agora)''이라고 이름붙이려고 했다. 광장은 그리스어로 열린 시장이나 공개 집회장을 뜻한다. 마크 밀러 (Mark Miller) 와 에릭 드렉슬러 (Eric Drexler) 의 생산적인 글, ``광장 시스템'' 에 관한 논문들은 계량 생태학과 비슷한 시장의 속성들을 묘사함으로써 5년 후에 리눅스를 알게 되었을 때 오픈 소스 문화에서 일어나는 현상들을 그에 비유하여 생각할 수 있도록 도와주었다. 이 글들은 웹의 다음 주소에서 구할 수 있다. http://www.agorics.com/agorpapers.html

## Acknowledgements
## 감사의 글

This essay was improved by conversations with a large number of people who helped debug it. Particular thanks to Jeff Dutky <dutky@wam.umd.edu>, who suggested the ``debugging is parallelizable'' formulation, and helped develop the analysis that proceeds from it. Also to Nancy Lebovitz <nancyl@universe.digex.net> for her suggestion that I emulate Weinberg by quoting Kropotkin. Perceptive criticisms also came from Joan Eslinger <wombat@kilimanjaro.engr.sgi.com> and Marty Franz <marty@net-link.net> of the General Technics list. Glen Vandenburg <glv@vanderburg.org> pointeed out the importance of self-selection in contributor populations and suggested the fruitful idea that much development rectifies `bugs of omission'; Daniel Upper <upper@peak.org> suggested the natural analogies for this. I'm grateful to the members of PLUG, the Philadelphia Linux User's group, for providing the first test audience for the first public version of this essay. Paula Matuszek <matusp00@mh.us.sbphrd.com> enlightened me about the practice of software management. Phil Hudson <phil.hudson@iname.com> reminded me that the social organization of the hacker culture mirrors the organization of its software, and vice-versa. John Buck <johnbuck@sea.ece.umassd.edu> pointed out that MATLAB makes an instructive parallel to Emacs. Russell Johnston <russjj@mail.com> brought me to consciousness about some of the mechanisms discussed in ``How Many Eyeballs Tame Complexity.'' Finally, Linus Torvalds's comments were helpful and his early endorsement very encouraging.

이 글은 많은 사람들과의 대화를 통해 잘못을 수정하는데 도움을 받았다. 특별히 제프 덧키(Jeff Dutky : <dutky@wam.umd.edu>) 에게 감사한다. 그는 ``디버깅은 병렬처리가 가능하다'' 는 말을 제안해 주었고 그로부터 이어지는 분석을 발전시키는데 도움을 주었다. 낸시 레보비츠(Nancy Lebovitz : <nancyl@univers.digex.net>) 에게도 감사한다. 그녀는 크로포트킨을 인용하여 내가 와인버그를 흉내내도록 도와주었다. General Technics 리스트의 조안 에슬링거 (Joan Eslinger <wombat@kilimanjaro.engr.sgi.com>) 와 마티 프란츠 (Marty Franz <marty@net-link.net>) 도 예리한 비판을 보내주었다. 폴 에거트 (Paul Eggert <eggert@twinsun.com>)는 GPL 과 시장 모델의 상충되는 점을 알려주었다. 이 글의 첫 번째 공개버전의 첫 번째 시험적인 청중이 되어준 필라델피아 리눅스 사용자 모임 (PLUG : Philadelphia LInux User's Group) 의 멤버들에게 감사한다. 마지막으로 리누스 토발즈의 논평은 도움이 되었으며 초기에 그가 해준 추천은 매우 격려가 되는 것이었다.
