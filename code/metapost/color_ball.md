---
title: Color Ball
---

```javascript
outputformat := "eps";
prologues := 3;
outputtemplate := "%j-%c.eps";
defaultscale := 2pt;
u:= 5cm;

beginfig(1);

vardef hsv_color(expr h,s,v) =
    % following wikipedia article on "HSL and HSV"
	save chroma, hh, x, m;
	chroma = v*s;
	hh = h/60;
	x  = chroma * (1-abs(hh mod 2 - 1));
	m  = v - chroma;
	if     hh < 1: (chroma,x,0)+(m,m,m)
	elseif hh < 2: (x,chroma,0)+(m,m,m)
	elseif hh < 3: (0,chroma,x)+(m,m,m)
	elseif hh < 4: (0,x,chroma)+(m,m,m)
	elseif hh < 5: (x,0,chroma)+(m,m,m)
	else:          (chroma,0,x)+(m,m,m)
	fi
enddef;

pickup pencircle scaled 0.1pt;

path p[];
path q[];
path l[];
path m[];
path r[];
path n[];
path c[];
path d[];
path s[];
p0 = (0,1u)..(-1u,0)..(0,-1u);
q0 = p0 shifted(2.5u,0);
r0 = p0 shifted(2.5u,-2.5u);
for i=1 upto 6:
	p[i] = (0,1u)..((2*i-7)/6.0*u,0)..(0,-1u);
	q[i] = p[i] shifted(2.5u,0);
	r[i] = p[i] shifted(2.5u,-2.5u);
endfor
p7 = (0,1u)..(1u,0)..(0,-1u);
q7 = p7 shifted(2.5u,0);
r7 = p7 shifted(2.5u,-2.5u);

for i=0 upto 7:
	l[i] = (-1u,cosd(180.0/7*i)*u)--(1u,cosd(180.0/7*i)*u);
	m[i] = l[i] shifted(2.5u,0);
	n[i] = l[i] shifted(2.5u,-2.5u);
endfor;

for i=0 upto 4:
	c[i] = ((0,1.0/4.0*i*u)..(-1.0/4.0*i*u,0)..(0,-1.0/4.0*i*u)..(1.0/4.0*i*u,0)) shifted (0,-2.5u);
	d[i] = ((0,-1.0/4.0*i*u)..(1.0/4.0*i*u,0)..(0,1.0/4.0*i*u)..(-1.0/4.0*i*u,0)) shifted (0,-2.5u);
endfor;

for i=0 upto 12:
	s[i] = ((0,0)--(cosd(30*i)*2*u,sind(30*i)*2*u)) shifted (0,-2.5u);
endfor;

for h=0 upto 6:
	for s=1 upto 7:
		fill buildcycle(p[h],l[s],p[h+1],l[s-1]) withcolor hsv_color((6-h)*30,s/7.0,1-s/15.0);
		fill buildcycle(q[h],m[s],q[h+1],m[s-1]) withcolor hsv_color((12-h)*30,s/7.0,1-s/15.0);
		fill buildcycle(r[h],n[s],r[h+1],n[s-1]) withcolor hsv_color((h+6)*30,s/7.0,1-s/15.0);
	endfor
endfor

for h=1 upto 6:
	for t=1 upto 4:
		fill buildcycle(s[h],d[t],s[h-1],d[t-1]) withcolor hsv_color(h*30,t/4.0,1);
	endfor
endfor

for h=7 upto 12:
	for t=1 upto 4:
		fill buildcycle(s[h],c[t],s[h-1],c[t-1]) withcolor hsv_color(h*30,t/4.0,1);
	endfor
endfor

endfig;

end.
```
