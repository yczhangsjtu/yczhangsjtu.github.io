---
title: Peterson Graph
---

```javascript
outputformat := "eps";
prologues := 3;
outputtemplate := "%j-%c.eps";
%defaultscale := 1.5;
u:= 3cm;


beginfig(1);

pair p[],q[];
for i=0 upto 4:
	x := u*sind(72*i);
	y := u*cosd(72*i);
	p[i]=(2*x,2*y);
	q[i]=(x,y);
endfor

pickup pencircle scaled 5pt
draw p0--p1--p2--p3--p4--cycle withcolor blue;
draw q0--q2--q4--q1--q3--cycle withcolor blue;
for i=0 upto 4:
	pickup pencircle scaled 5pt
	draw p[i]--q[i] withcolor blue;
	label.top(("b"&decimal(i)) infont defaultfont scaled 1.5, 0.5[p[i],q[i]])
		withcolor green;
	pickup pencircle scaled 20pt
	drawdot p[i] withcolor red;
	drawdot q[i] withcolor red;
	label.top(("p"&decimal(i)) infont defaultfont scaled 1.5, p[i]+(0,10));
	label.top(("q"&decimal(i)) infont defaultfont scaled 1.5, q[i]+(0,10));
endfor


endfig;

end.
```
