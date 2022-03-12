---
layout:     post
title:      ATENEA | Podrías descifrar el mensaje sin la clave?
date:       2022-03-12 10:00:00
summary:    Writeup to solve an old challenge related to basic cryptography.
categories: CTF
thumbnail: flag
tags:
 - CTF
 - Atenea
 - Cryptography
---
>Owpty&c%l, §&l&ó$ n hk&%k&rqrtl
>Sx hk&$e&hqs %wpty&c% tl e%$yk&wx&k q sq §tp%kq rts $&cts rt lt*xk&rqr rt s%l l&lyt§ql rt &$z%k§qe&ó$ rt sql yktl qr§&$&lykqe&%$tl húws&eql tb&lyt$ytl t$ Elhqñq >(*t$tkqs, qxy%$ó§&eq n s%eqs).
>Sx §&l&ó$ tl e%$ctky&klt t$ ts et$yk% rt qstkyq $qe&%$qs jxt e%%htkt n qnxrt q y%rql sql qr§&$&lykqe&%$tl húws&eql q ktlh%$rtk rt z%k§q káh&rq n tz&e&t$yt q s%l &>$e&rt$ytl rt lt*xk&rqr jxt hxr&tkq$ lxk*&k n qzk%$yqk rt z%k§q qey&cq sql $xtcql q§t$qmql q sql jxt /%n t$ ríq tlyá$ tbhxtlyql.
>Pqkq e%$yk&wx&k q tlyq §tp%kq rts $&cts rt lt*xk&rqr, ts CCN-CERT %zktet lxl ltkc&e&%l q y%r%l s%l ktlh%$lqwstl rt Tte$%s%*íql rt sq I$z%k§qe&ó$ rt sql r&ztkt$ytl >qr§&$&lykqe&%$tl húws&eql q ykqcél rt e&$e% *kq$rtl sí$tql rt qeyxqe&ó$:
>S%h%kyt n e%%kr&$qe&ó$ hqkq ts ykqyq§&t$y% rt cxs$tkqw&s&rqrtl n sq ktl%sxe&ó$ rt &$e&rt$ytl rt lt*xk&rqr jxt yt$*q$ sq Ar§&$&lykqe&ó$ Gt$tkqs rts Elyqr%, sql qr§&>$&lykqe&%$tl rt sql e%§x$&rqrtl qxyó$%§ql, sql t$y&rqrtl jxt &$yt*kq$ sq Ar§&$&lykqe&ó$ L%eqs n sql E$y&rqrtl rt Dtkte/% húws&e% e%$ htkl%$qs&rqr pxkír&eq hk%h&q c&>$exsqrql % rtht$r&t$ytl rt exqsjx&tkq rt sql qr§&$&lykqe&%$tl &$r&eqrql. Es CCN-CERT, q ykqcél rt lx ltkc&e&% rt qh%n% yée$&e% n rt e%%kr&$qe&ó$, qeyxqká e%$ sq >§áb&§q etstk&rqr q$yt exqsjx&tk q*ktl&ó$ kte&w&rq t$ s%l l&lyt§ql rt &$z%k§qe&ó$ rt sql qr§&$&lykqe&%$tl húws&eql. Pqkq ts ex§hs&§&t$y% rt s%l z&$tl &$r&eqr%l t$ >s%l hákkqz%l q$ytk&%ktl lt h%rká$ kteqwqk s%l &$z%k§tl rt qxr&y%kíq rt s%l l&lyt§ql qzteyqr%l.
>Fts&e&rqrtl, yx q$l&qr% ytl%k% tl: zsq*{t53z00w7124266z8et045t27q873220t}
>I$ctly&*qe&ó$ n r&cxs*qe&ó$ rt sql §tp%ktl hkáey&eql l%wkt lt*xk&rqr rtsq &$z%k§qe&ó$ t$ykt y%r%l s%l §&t§wk%l rt sql qr§&$&lykqe&%$tl húws&eql. C%$ tlyq z&$qs&>rqr, sql ltk&tl rt r%ex§t$y%l CCN-STIC %zktetká$ $%k§ql, &$lykxee&%$tl, *xíql n kte%§t$rqe&%$tl hqkq qhs&eqk ts ENS n hqkq*qkq$y&mqk sq lt*xk&rqr rt s%l l&lyt§ql >rt Tte$%s%*íql rt sq I$z%k§qe&ó$ t$ sq Ar§&$&lykqe&ó$.
>F%k§qe&ó$ rtly&$qrq qs htkl%$qs rt sq Ar§&$&lykqe&ó$ tlhte&qs&lyq t$ ts eq§h% rt sq lt*xk&rqr TIC, qs %wpty% rt zqe&s&yqk sq qeyxqs&mqe&ó$ rt e%$%e&§&t$y%l n rt >s%*kqk sq lt$l&w&s&mqe&ó$ n §tp%kq rt lxl eqhqe&rqrtl hqkq sq rtytee&ó$ n *tly&ó$ rt &$e&rt$ytl.
>I$z%k§qe&ó$ l%wkt cxs$tkqw&s&rqrtl, qstkyql n qc&l%l rt $xtcql q§t$qmql q s%l l&lyt§ql rt &$z%k§qe&ó$, kte%h&sqrql rt r&ctklql zxt$ytl rt kte%$%e&r% hktly&*&%, &>$esx&rql sql hk%h&ql.
>I§hxsl% rt $xtcql eqhqe&rqrtl rt ktlhxtlyq q &$e&rt$ytl t$ sql AAPP. Es CCN rtlqkk%ssqká x$ hk%*kq§q jxt %zktmeq sq &$z%k§qe&ó$, z%k§qe&ó$, kte%§t$rqe&%$tl n />tkkq§&t$yql $tetlqk&ql hqkq jxt sql qr§&$&lykqe&%$tl húws&eql hxtrq$ rtlqkk%ssqk lxl hk%h&ql eqhqe&rqrtl rt ktlhxtlyq q &$e&rt$ytl rt lt*xk&rqr.