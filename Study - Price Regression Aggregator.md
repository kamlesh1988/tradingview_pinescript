Study - Price Regression Aggregator. Uses 6 linear regressions, slope and n forecast to estimate possible confluence points. 

![PRAggregator example 1](https://i.imgur.com/prjwhSx.png)

![PRAggregator example 2](https://i.imgur.com/fxkMq2m.png)

Yes, i left my artistic spirit out for a while.

```

//Price regression Agreggator
//-------------------------------------------------
//How it works:
//    It uses 6 linear regression from time past to get an estimated point in future time, and using transparency, those areas that are move "visited" 
//        by those 6 different regressions and maybe more probable to be visited by the price (in fact if you zoom out you will see that price normally is around 
//        the lighter zones) have more aggregated painted colors, the transparency is lower and well, the lighter area should be more probable to be visited by the 
//         price should we put any faith on linear regression estimations and even more when many of them coincide in several points where the color is more aggregated.
//    If the "I" (the previous regressions increment) is too low, then we will have huge spikes as the only info gathered from the oldest linear regresssion will be 
//        within the very same trend we are now, resulting in "predictions" of huge spikes in the trend direction. (all regressions estimating on a line pointing to infinite)
//    If the "I" is high enough (not very or TV won't be able to display it) then you will get somewhat a "vectorial" resultant force of many linear regressions giving 
//        a more "real prediction" as it comes from tendencies from higher timeframes. E.g. 12 hours could be going down, 4h could be going sideways, 30m could be going up.
//    Anyway, an experiment.
//          contact tradingview -> hecate   . The idea and implementation is mine.
//
//Note: transparency + 10 * tranparencygradient cannot be > 100 or nothing will be displayed
//Note2: if the Future increment (how many lines are displayed to the right of the actual price ) are excessive, it will start to do weird things.
//Note3: two times the standard deviation statistically correponds to a probability of 95%. We are calculating Top and Bot with that amount above and below. So anything
//              inside those limits is more probable and if we are out of those limits it should fall back soon. Increase the number of times the std deviation as desired
//              there are calculators in the web to translate number of times std dev to their correspondent probability.
//Note4: As we use backwards in time linear regressions for our "predictions" we lose responsiveness. Those old linear regressions are weighted with less value than more recent ones.
//Note5: In the code i have included many color combinations (some horrible :-) )
//Note6: This was an experiment while i was quite bored although ended enjoying playing with it. 




study("Price Regression Agreggator",overlay=true)

h=high
c=close
l=low

P = input(title="Initial Regression Period", type=integer, defval=100,minval=2,maxval=100,step=1) 
I = input(title="Increment of previous regression (up to 6 times backwards)",type=integer,defval=100,minval=3,maxval=100,step=1) 
FI= input(title="Future increment (up to 6 times this value forward)",type=integer,defval=10,minval=2,maxval=20,step=1) 
t = input(title="Transparency (less is less transparent)",type=integer,defval=60,minval=0,maxval=50,step=1) 
m = input(title="Transparency gradient (notice that ('Transparency'+ this value*10 cannot be >100)", type=integer,defval=4,minval=0,maxval=9,step=1) 
y = input(title="Linewidth",type=integer,defval=0,minval=0,maxval=4,step=1)
st = input(title="Times Standard Deviation",type=float,defval=2.5,minval=0,maxval=5) 
shift=input(title="Shift colors or center them [0/1]",type=integer,defval=0,minval=0,maxval=1,step=1)


FutC(a,firstperiod,increment,futurecandles)=>(4*increment*linreg(a,firstperiod,futurecandles)+3*increment*linreg(a,firstperiod+increment,futurecandles)+2*increment*linreg(a,firstperiod+1*increment,futurecandles)+1*increment*linreg(a,firstperiod+3*increment,futurecandles))/((4+3+2+1)*increment)

// RED TO YELLOW
//col6=#ffff11
//col5=#ffcc11
//col4=#ff9911
//col3=#ff6611
//col2=#ff3311
//col1=#ff1111

// RED TO WHITE
//col6=#aaaaaa
//col5=#aa9999
//col4=#aa7777
//col3=#aa5555
//col2=#aa3333
//col1=#aa1111


// YELLOW TO RED
//col1=#ffff11
//col2=#ffcc11
//col3=#ff9911
//col4=#ff6611
//col5=#ff3311
//col6=#ff1111

// AQUA
//col6=#fd1111
//col5=#dfd111
//col4=#1dfd11
//col3=#11dfd1
//col2=#111dfd
//col1=#1111df

// SUDOKU DARK (like a dark rainbow)
//col6=#612345
//col5=#561234
//col4=#456123
//col3=#345612
//col2=#234561
//col1=#123456

// SUDOKU LIGHT (another rainbow)
//col1=#d3579b
//col2=#bd3579
//col3=#9bd357
//col4=#79bd35
//col5=#579bd3
//col6=#3579bd



// ALL WHITES WITH GRADIENTS
col1=#ffffff
col2=#ffffff
col3=#ffffff
col4=#ffffff
col5=#ffffff
col6=#ffffff

// ALL BLACKS WITH GRADIENTS
//col1=#000000
//col2=#000000
//col3=#000000
//col4=#000000
//col5=#000000
//col6=#000000



top=h+stdev(h,P)*st      
bot=l-stdev(l,P)*st


h1=FutC(top,P,I,1*FI)
h2=FutC(top,P,I,2*FI)
h3=FutC(top,P,I,3*FI)
h4=FutC(top,P,I,4*FI)
h5=FutC(top,P,I,5*FI)
h6=FutC(top,P,I,6*FI)

l1=shift==1?FutC(bot,P,I,1*FI):FutC(bot,P,I,6*FI)
l2=shift==1?FutC(bot,P,I,2*FI):FutC(bot,P,I,5*FI)
l3=shift==1?FutC(bot,P,I,3*FI):FutC(bot,P,I,4*FI)
l4=shift==1?FutC(bot,P,I,4*FI):FutC(bot,P,I,3*FI)
l5=shift==1?FutC(bot,P,I,5*FI):FutC(bot,P,I,2*FI)
l6=shift==1?FutC(bot,P,I,6*FI):FutC(bot,P,I,1*FI)

//xy0=plot(close, style=line, linewidth=2, color=col1,offset=0,transp=t+1*m)
x1=plot(h1, style=line, linewidth=y, color=col1,offset=1*FI,transp=t+1*m)
x2=plot(h2, style=line, linewidth=y, color=col2,offset=2*FI,transp=t+2*m)
x3=plot(h3, style=line, linewidth=y, color=col3,offset=3*FI,transp=t+3*m)
x4=plot(h4, style=line, linewidth=y, color=col4,offset=4*FI,transp=t+4*m)
x5=plot(h5, style=line, linewidth=y, color=col5,offset=5*FI,transp=t+5*m)
x6=plot(h6, style=line, linewidth=y, color=col6,offset=6*FI,transp=t+6*m)
y1=plot(l1, style=line, linewidth=y, color=col1,offset=1*FI,transp=t+1*m)
y2=plot(l2, style=line, linewidth=y, color=col2,offset=2*FI,transp=t+2*m)
y3=plot(l3, style=line, linewidth=y, color=col3,offset=3*FI,transp=t+3*m)
y4=plot(l4, style=line, linewidth=y, color=col4,offset=4*FI,transp=t+4*m)
y5=plot(l5, style=line, linewidth=y, color=col5,offset=5*FI,transp=t+5*m)
y6=plot(l6, style=line, linewidth=y, color=col6,offset=6*FI,transp=t+6*m)


fill(x1,y1,color=col1,transp=t+1*m)
fill(x2,y2,color=col2,transp=t+2*m)
fill(x3,y3,color=col3,transp=t+4*m)
fill(x4,y4,color=col4,transp=t+6*m)
fill(x5,y5,color=col5,transp=t+8*m)
fill(x6,y6,color=col6,transp=t+10*m)


```

