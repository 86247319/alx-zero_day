### DHCP :
- اول حاجه هعملها انى اخلى راوتر هوا Dhcp server 
-  add ip address
- service dhcp 
- ip dhcp excluded-address 192.168.1.2                             ده لحد ipكده انا بقوله ماتديش       
- ip dhcp excluded-address 192.168.1.3 192.168.1.20  كده بقوله الرينج ده ماتديش منه لاجهزة وابدا من 21
- ip dhcp pool LAN1
- network 192.168.1.0 /24
- ip dhcp pool LAN2
- network 10.0.0.0 /24
- اللى فوق دول عشان لو انا عندى مثلا 3 شبكات وعايز اوزع عليهم كلهم ip بس كل حاجه لوحدها lan يعنى
- وهوا بيدى ip على اساس اى هوا جاى من انهى interfaceوعلى اساسها يده من pool اللى تبع الشبكه دى
[[Pasted image 20260606212255.png]]

- DHCP(dhcp-config)#dns-server 8.8.8.8 2.2.2.2 4.2.2.3
- DHCP(dhcp-config)#default-router 192.168.1.1
- DHCP(dhcp-config)#domain-name test.com 
- كل الحاجات اللى فوق دى وزعتها باستخدام DHCP
---

IOU1#show ip dhcp snooping binding
ده بيعرفك ip , اللى اخدته وال mac بتاعه واخدته من انهى interface
 IOU1(config)#ip dhcp snooping database ?
يفضل انك تنقله للمكان اللى تحدده من الاوبشنز دى 

---
in router
- add dhcp for client
- Client(config-if)# ip add dhcp                    dhcp من ip كده انا قولتله خد 
- Client(config-if)# no shut
- لو انا عايز الراوتر ياخد ip تانى اقفل interface وارجع اكتب الاوامر تانى


- ![[Pasted image 20260606223832.png|523]]
- اللى جمب identfirer ده username اللى انا جايبه من show ip dhcp binding بتاع جهاز الطابعه مثلا
- الاوامر اللى فوق دى لو انا عندى طابعة مش بتاخد ip static ف احط ip بتاعه فى pool محدده
- ![[Pasted image 20260606224109.png|444]]
كده يعنى الغى حجز ip ده ووزعه تانى عادى
- show ip dhcp binding 
- show ip dhcp pool
- debug ip dhcp server packet
---
add ip for pc in dhcp                       ip dhcp اكتب
pc in config   gateway ماتنساش تضيف  ip لما اجى ايده

ip  192.168.0.5/24 192.168.0.1

---
- بيقولك الافضل لما تيجى تحط شرط على ip محدد مثلا وهتبطق بعده حاجه عامة على بقيت ips حط الخاص الاول زى حته permit اللى تحت
---
### ACL (access control list):
### Standard type :
- اول حاجه اعملها اضيف routing table الاول و ips طبعا عشان الشبكات تقدر تشوف بعض هضيف مثلا ospf على كل الراواتر واعرف pc كمان وبعده ابقا اضيف ACL 
- r1(config)#access-list 1 ?
- r1(config)#access-list 1 permit 10.0.0.0 0.0.0.255        (ip +wild) ده لشبكة كاملة 
-  r1(config)#access-list 1 permit  host 10.0.0.3              واحد ip ده لو عايز اعرف 
- r1(config)#access-list 1 deny  host 11.0.0.6 كده منعته انه يدخل على الشبكة
-  r1(config)#access-list 1 permit any        وفى دى بس مش محببه
- r1(config)#int f0/1
- r1(config-if)# ip access-group 1 in      f0/1 اللى انا عاملها فوق على list 1كده انا بقول للراوتر طبقلى  
- in دى معانها الحاجات اللى جايه للراوتر ,out اللى خارج من الراوتر 
- r1(config-if)# no ip unreachables دى عشان مايظهرش نوع الخطا بالضبط عشان الهاكر مايستغلوش
- show ip access-list
-  r1(config)#access-list 1 remark دى اكنك عايز تضيف كومنت معين
- do show run | in access-list

---
standard, extend عيوبه كتير مثلا تحت ودى برضه فى 
-  r1(config)#no access-list 1 permit any           كده هوا حذف كل الحاجات اللى مرتبطة برقم 1
- بس هيا كده علفكرة مش كلها اتمسحت لازم اعمل اللى تحت ده عشان لو عملت حاجه فى المستقبل برقم 1
r1(config-if)# ip access-group 1 in

---

![[Pasted image 20260608140310.png|241]]

---
### Extended number ACL :
الفرق ببينه وبين standard انه ليه src ,dst وبيحدد نوع ال protocol  : التانى بيحدد src بس

- access-list 100 permit tcp host 10.0.0.1 host 10.0.0.2 eq telnet or 23 
- Extended<100-999> , (tcp ,udp or need type) نحدد نوع بروتكول , 
- بقوله الكلام ده على الترافك اللى جاى من 10.1 ل 10.2 و eq دى يعنى = نوع البروتكول اللى هيشتغل مع tcp اللى هوا فى حالتنا هنا telnet واقدر اكتب رقمه بس
- r2(config)#access-list 100 deny icmp host 10.0.0.1 host 10.0.0.2 echo                 ping كده بمنع 
- access-list 100 permit 89 any any    89=ospf   ,   src ,dst كده بقوله اسمح بيه بس منغير 
- do show access-list
- r2(config)#int f0/1              interface هنا بقى هطبق الكلام اللى عملته فوق على 
-  r2(config-if)#ip access-group 100 in بقوله الكلام ده على الترافك اللى داخلك
- الصورة اللى تحت على interface تانيه
- ![[Pasted image 20260608143606.png|372]]![[Pasted image 20260608143659.png|139]]
-  r2(config)#access-list 101 permit ip any any  كده انا سمحت بكل البروتكولات على 101 بس
- do show access-list هتشوف دى هتلاقى كل اللى سمحته او منعته
-  r1(config)#no access-list 101 permit any           كده هوا حذف كل الحاجات اللى مرتبطة برقم 1
- دول بقى هيتحلوا ب named ACL
---
#### Named ACL :
- دى بقى بتشتغل ازاى اكن عندى list و ب(بتضيف فى اخر اللست)append(دى بحدد هضيف فين) insert
- وعندها ميزه انها بتسيب بين كل صف مثلا والتانى 10 شغاله كده 10,20,30 وانتا بت insert بينهم 
- وهنا بقى الفرق انك لما تمسح امر تمسحه هوا بس مش كله
- ![[Pasted image 20260608145421.png|476]]
- بيشتغل كده بتحدد نوعه (standard,extended) وبتكتب بقى الكلمه اللى انتا عايزها
- ولما تعوز تمسح تكتب الامر او اكتب sequence number
  ![[Pasted image 20260608145835.png|483]]
- ولما اعوز ا insert  اكتب sequence number ,والامر اللى عاوزه   
- 26 permit host 10.1.1.1 
- ip access-list resequence CISCO 10 20 كده خليت الفرق ببينهم 20 عشان لو عايز اكتب حاجانت كتيرة
- يفضل بعد اما خلصت كل اللى فوق ده 
- access-list 100 deny ip any any log كده لو ترافك قابل دى هيبعتلى يقولى دى المشكلة

![[Screenshot 2026-06-08 152159.png|535]]

debug ip acket detail 100 كده بفلتره عشان الراوتر مايهنجش لو استخدمت العام
[[acl revision.jpg]]

---
لو عايز اضيف خاصية Telnet  ,SMB على الجهاز عندى وحاجات تانيه كتير ادخل على المسار ده
==Control Panel\Programs\Programs and Features وافعل Turn windows features== 
هلاقيها فى اول الصفحة فوق

---
### NAT :
![[Pasted image 20260608210339.png|425]]
static NAT :
- اول حاجه اعملها لازم اضيف routing هنا هستخدم static route مثلا
- r1(config)#ip nat inside source static 192.168.1.100 50.0.0.1 
كده انا حددت نوع النات وجاي منين وال ip بتاع الجهاز بتاعى مثلا و  public ipاللى شاريه
- r1(config)#int f0/0
- r1(config-if)#ip nat inside
- r1(config)#int f0/1
- r1(config-if)#ip nat outside
- كده فوق انا حددت الطريقة اللى هطلع بيه ب public ip غيره هيستخدم الراوتنج عادى
- ![[Pasted image 20260608220141.png|563]]
- احنا لغينا الراوتينج اللى فوق ليه عشان الراوتر بيشوف private ip وانا عايزه يشوف public مش العكس
- هنا انا اديه ال ip ال public فى route فكده هيشتغل
- show ip  nat translations
- 50.0.0.0  loop back  برضه فعنعملها اكنها  وهمى  routing table الشبكة دى لسه برضه مش ظاهرة فى 

---
#### dynamic NAT :
- لو استخدمت overload هنا كان كل ip in pool كان هيخدم على 65000 ip
1. r1(config)#ip nat pool WAN 50.0.0.1 50.0.0.3 prefix-length 30 هنا اديتها اسم وحطيت رينج للشبكات وضيفت subnetmask  prefix ممكن اضيف من الطريقة دى برضه بدل netmask 255.255.255.252
- بعد كده هحدد ips اللى هعمل عليها الاكشن دى واحددها من خلال ACL
1. r1(config)#access-list 1 permit 192.168.1.0 0.0.0.255     NAT انا كده بححدهم عشان 
- غير كده هيطلوا راوتنج عادى
3. r1(config)#ip nat inside source list 1 pool WAN          سوا (nat,acl) وكده انا ربطت
- بعد كده ادخل على interface 2 واحدد الداخل والخارج 
1.  r1(config)#int f0/0
2. r1(config-if)#ip nat inside
3.  r1(config)#int f0/1
4. r1(config-if)#ip nat outside
- بعد كده هنطبق الراوتنج عشان اقدر اشوف public ip 
1.  r1(config)#ip route 50.0.0.0 255.255.255.0 11.0.0.1
2. show ip route بتاع الراوتر اللى طالع مكانه زى 11.0 مثلا هيشتغل عادى ipهلاقيه ظهر ولو بنجت على 
3. show ip nat translations
- ip nat pool WAN 50.0.0.1 50.0.0.3 prefix-length 30 add-route  
- حته الاد دى بيتضيفه فى الراواتنج ك استتك بتفيد برضه

---
### PAT :
1. r1(config)#access-list 1 permit 192.168.1.0 0.0.0.255 
2. r1(config)#ip nat inside source list 1 interface f1/0 overload      هنا بحددله  هيطلع للنت من        جهاز واحد اللى هيطلع على النت overload و من غير interfaceانهى 
3. r1(config)#int f0/0
4. r1(config-if)#ip nat in
5.  r1(config)#int f1/0
6. r1(config-if)#ip nat out
---
#### NAT in camera
![[Screenshot 2026-06-09 130000.png|488]]

1. r1(config)#ip nat inside source static tcp 192.168.1.100 80 50.0.0.1 2000     as cam1
2. r1(config)#ip nat inside source static tcp 192.168.1.101 80 50.0.0.1 2002     as cam2
3. r1(config)#ip nat inside source static tcp 192.168.1.102 80 50.0.0.1 2003     as cam3
4. r1(config)#ip nat inside source static tcp 192.168.1.103 80 50.0.0.1 2004    as cam4
5. على حسب عدد الكاميرات بقى وهيا شغاله على بورت 80 واجيب اغير بقى ip و port اللى داخل 
6. r1(config)#int f1/0
7. r1(config-if)#ip nat in
8.  r1(config)#int f0/0
9. r1(config-if)#ip nat out

- ممكن اعمل نفس الكلام بالضبط باختلاف البورت بس  ده على سيرفر ل remote desktop مثلا  port 3389
- ![[Pasted image 20260609131137.png|356]]
---
### VLAN :
اهمية اية : انها تقسم الشبكة عندى لعدده اقسام (it,sales,hr) كده يعنى وكل واحده فيهم فى vlan معينة 
بس هيبقى فيه مشكله انا عايز vlan تشوف بعضها فهيتوصلوا ببعض ب routing بس كل جهازة هيشوف التانى ك unicast مش broadcast زى الاول 
- vlan range (2-1001) for static    ,extended(1006,4000)not used many
##### config:
show vlan brief
IOU1(config)#vlan 2 hr
IOU1(config)#vlan 3-7 كده كريت الرينج ده كله  بس هنا مش هينفع نديهم اسم السويتش يدى من عنده
IOU1(config-vlan)exit

IOU1(config)#int e0/0                        pc معينه لازم ادخل عليها واعمل كده ل int كده عشان اطبقه على 
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport access vlan 7                   اللى عايزه ينتمى ليها vlan ده رقم 7

IOU1(config)#int e0/1                             
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport access vlan 20
دلوقتى لو جيت ا  ping بينهم مش هيتصلوا  عشان كل واحده بقت فى vlan لوحدها

IOU1(config)#int range e3/0 - 3              وهطبق عليهم كلهم مره واحده interface كده ان دخلت على 3 
IOU1(config)#int range e1/3, e2/3 , e2/2          دول لو مش فى نفس الرينج

IOU1(config-if-range)#switchport mode access
IOU1(config-if-range)#switchport access vlan 30
لو جيت حذفت vlan 30 كل interfaces اللى تبعها بقوا homeless مشردين مش هيستخدموا  الا لو انشأت vlan 30 تانى هيرجعوا direct علطول

Switch(config)# no vlan 10   عشان نمسحها

IOU1#erase startup-config
IOU1#reload                                                 بالامرين دول زى الراوترdefualt كده رجعت السوتش على 
بس لمااعمله ال vlan مش هتتمسح لانها بتتخزن فى vram in startup config
Switch#delete vlan.dat               هتتمسح ده على السوتش الحقيقىvlan بالامر ده كده كل 

![[Pasted image 20260610203826.png|376]]

بيقولك مفيش غير native vlan واحده بس اللى هيا vlan1 وبتتبعت un tagged لنفس vlan1
![[Pasted image 20260611211716.png|384]]
int modes:
- ==access== (the port belongs to only one VLAN) ==1 vlan and many pcs==
- ==trunk== (the port belongs to ALL VLANs) ==between sw1 and sw2==
- ==DTP== ( dynamic trunking protocol ) ماردش  access ده بيشتغل اوتوماتك يعنى لو الجهاز رد عليه 
     يشتغل trunk
- type dynamic : use between switch

	auto : لو السوتش اللى قصاده بدا اتصال يشتغل ما بدأش هيفضل مستنى (يعنى هنا ماينفعش 2 auto)

	desirable :  ده بيدأ اتصال ويبعت للتانى الاول مش بيفضل مستنية يبعت
	
config:
IOU1#show interface trunk         ولا لا  trunk شغاله interface ده بيشوف كل 
IOU1#show interface e0/0 trunk  بيعنها ويشوف حالتها اى interface هنا بيوريك 
Encapsulation : negotiate      isl بس فى الاخر بيشتغل  (isl , dot 1 q)معنها انه بيدعم 

IOU1#show interface switchport     او لو حاجه عايز تزودها interface ده بيعرف كل الحاجات اللى معموله ل
IOU1(config-if)#switchport mode trunk      بس dot 1q يبقى شغال الاتنين لو مداش يبقى error لو اداك 

IOU1(config-if)#switchport trunk encapsulation ? كده  isl وده بيوريك الخياارت اللى عندك تشتغل بيها
! use dot1q
IOU1(config-if)#switchport trunk encapsulation dot1q
IOU1(config-if)#switchport mode trunk
IOU1(config-if)#switchport nonegotiate
IOU1(config-if)#end
- لو انا عملت سوتش trunk اعمل التانى زيه 
- ممكن اعمل واحد trunk واسيب التانى auto بس ساعتها اسيب dynamic شغال اللى هوا  negotiate ازاى كده 
- IOU1(config-if)#no switchport no
- IOU1(config-if)#no switchport nonegotiate

IOU1(config-if)#switchport trunk allowed vlan 1 add 10      كده هيسمح للاتنين يخرجوا 
vlan 10 مش ضافها معاه vlan 1 على  overwrite لو كنت عملتها كده كان عمل 

IOU1(config-if)#switchport trunk native vlan 1 الافضل انك تخليه كده مش تعدل عليه
![[Pasted image 20260612202701.png|266]]
 for secutity   واحد  pc ان اى بورت متوصل ب 
 switchport mode access           يستخدمه attackerعشان مفيش  dtp كده هوا منع بروتكول  
 ويبعته للسوتش التانى ويخليه يستخدم trunk
 ![[Pasted image 20260612212133.png|335]]

---
switching jobs

- mac address learning and building

- forward decision للداتا ولا لا يعنى يعديه forwardهنا بيسمح اذا كان يعمل 
 يعنى لو جايله جهازين على نفس للبورت مثلا مش هيعديه
- prevent switching loop caused by
	redundant topology
	
show processor cpu       switching loopاللى على السوتش او الراوتر قد اى لو عالى قوى  load ده بيعرفك 

---
#### STP (spanning tree protocol):

 فائدته: يمنع الـ Loops عن طريق إغلاق المسارات الزائدة ويحتفظ بها كمسار احتياطي. ده عشان السوتش ليه مسارات كتير 

switch=bridge  بيشتغل بنفس الطريقة بالضبط بس واحد هاردوير والتانى سوفت وير كان معمول الاول ل

- one root bridge per network      root switch يفضل يبقى فى منطقة core layer
معناها انه يبى عندى سوتش واحد ل root بيتحسب ازاى اقل mac واقل priority
- ==one root port== per non-root bridge
هنا بنحسب تكلفه كل مسار من root للى جبناه فوق ب اى path cost من كل interface بيعدى عليها ل dst
- one designated port per segment(كل سلكه)
وكل segment يبقى ليها one designated port يعنى int واحده بيتحسب بالاقل فى path cost للسوتشات باقية يعنى root اتحسب وفنحسب بقى للباقى
- block all others
هنا بقى بقيت الحاجات اللى ماتحسبلهاش حاجه يتعملها بلوك وممكن تحسبه كده : عدد السلوك-(num sw -1)
Spanning tree path cost :

![[Pasted image 20260613131551.png|248]]![[Pasted image 20260613134955.png|209]]![[Pasted image 20260613135234.png|223]]

---
cisco use pvst +

config :
IOU2(config)#spanning-tree mode pvst     use in 2 sw
IOU2(config)#do show spanning-tree 
لازم عشان اطبقة التوصيل بين السوتشين ب trunk                                           
pvst = ieee

IOU1(config)#vlan 2
IOU1(config)#int range e0/0 - 1
IOU1(config-if-range)#switchport trunk encapsulation dot1q
IOU1(config-if-range)#switchport mode trunk
IOU1(config-if-range)#end
IOU2(config)#spanning-tree vlan 2 root primary
IOU2#show spanning-tree vlan 2
IOU2#show span
IOU1#debug spanning-tree events
![[Pasted image 20260613161511.png|355]]


#### enhanced pvst :
IOU1(config)int f0/1
IOU1(config-if)#spanning-tree portfast 
الامر ده بيقولك لازم التوصيل يبقى not trunking يعنى ميكونش متوصل بسوتشين مع بعض مثلا فعشانه 
IOU1(config-if)##switchport mode access                                                                             يبقى كده
IOU1(config-if)#shut
IOU1(config-if)#no shut    كده هيورنى التغير اللى حصل للبورت
IOU1#show spanning-tree int e0/2 portfast              pc معناها انه يكنكت ب 
IOU1(config-if)#
IOU1(config-if)#spanning-tree portfast trunk                      غصب عنهtrunk ده لو انتا عايزه يشغل
بحيث انى كنت عايز اوصل server بسوتش
IOU1(config)#int e0/2
IOU1(config-if)#spanning-tree bpduguard enable
الامر ده عشان ميحصلش loop  لو حد مثلا شال كابل السيرفر ووصله بسوتش كده بقوا 2 sw متوصلين
IOU1#show interface status
لو status كانت err-disabled يبقى اللى عمل كده bpduguard
==IOU1#show interface status err-disabled==
 هيقولك السبب خلاه كده عشان ممكن ميكنش سببه bpduguard

IOU1(config)#errdisable recovery interval 30
IOU1(config)#errdisable recovery cause bpduguard
IOU1(config)#end           interface enable كده هوا عمل 
IOU1#show errdisable recovery
IOU1(config)#spanning-tree por
IOU1(config)#spanning-tree portfast default
ده لو انا عايز اشغله على كل البورت بس كده معناه ان اى int  اعملها access يشتغل معاها فى background
IOU1(config)#spanning-tree portfast bpduguard default
IOU1(config)#
ده لو انا عايز ا disable بورت معينه
IOU1(config-if)#spanning-tree portfast disable
IOU1(config-if)#spanning-tree bpduguard disable

![[Pasted image 20260613183445.png|331]]

#### Rapid STP (RSTP): اسرع واحسن
IOU1(config)#spanning-tree mode rapid-pvst                use  IN 2 SW
IOU1(config)#
IOU2(config-if)#spanning-tree link-type point-to-point                        عشان يشتغل بكفاءه عاليه

---
#### STP Optimization

STP Convergence Optimization:  هنا عايز اسرع البروتكول
> PortFast
> UplinkFast                   (1s to 3s) no timeوقع ياخد البلوك مكانه علطول فى  root ده لو البورت
> هوا متفعل فى rst علطول لكن لو انتا شغال +pvst لازم تفعله وهوا بيحمنى من المشاكل direct change
IOU3(config)#spanning-tree uplinkfast

> BackboneFast                RSTP  على defualt تفعله على كل السويتشات وهوا شغال   must ده 
بيحمينى من المشاكل indirect change
IOU3(config)#spanning-tree backbonefast
IOU3(config)#show spanning-tree summary 

STP Filters: بس استلم BPDU هنا المفروض السوتش مايستلمش 
> BPDU Guard
> BPDU Filter
> Root Guard

STP Loop Prevention: بس ماستلمش BPDU هنا المفروض السوتش  يستلم
> Loop Guard
> UDLD

---
LAN security attack :

- mac address flood
	mitigation: port security
	
-  DHCP spoofing         
			man in the middelمن عنده يعتبر  ips ويوزع هوا  ips بيسحب  rouge  ده بيشتغل ازاى 

	mitigation:
		DHCP snooping
sw1(config)# ip dhcp snooping                                                    rouge dhcp وده بيمنع 
sw1(config)# ip dhcp snooping vlan 1  عشان الامر ده تقيل على سوتش فيشتغل ع مهم vlanلازم تحددله 
sw1(config)# int e0/1
sw1(config-if)# ip dhcp snooping trust       كده بقوله ان الرساله اللى تخرج من هنا تخرج عادى 
sw1(config)# int e0/2        for client
sw1(config-if)# ip dhcp snooping limit rate 3 = num of packet
هنا بقوله هتخرج 2 packet بس اللى هما discover , request دول الرسالتين بتوع ip يعنى مش ياخد غير ip 1

- ARP spoofing
	mitigation:
		DAI (dynamic ARP inspection )
		
CDP attack :
يفضل انى اعمل disable for interface اللى رابطانى ب LAN او اى حاجه رابطنى ب اجهزة client

---
##### Configuring Port Security :
Secure MAC addresses are the following types:
· Static secure MAC addresses
· Dynamic secure MAC addresses
· Sticky secure MAC addresses

violation:
- protect :                  مختلف السوتش مش هيقبل الداتا دى mac معناه لو جاتلى داتا من 
- restrict :                   attack يقولك ان حد بيعمل عليك  sis log ده هيدك 
- shutdown (default)


IOU1(config)#int e0/0
IOU1(config-if)#shut                 
يفضل انى اعمل كده ليه عشان السوتش يمسح كل macs اللى عنده ولازم بعد اما اخلص config اعملno shut
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport port-security
IOU1(config-if)#switchport port-security ?
IOU1(config-if)#switchport port-security mac-address sticky                    dynamic هيدى ماكات 
IOU1(config-if)#switchport port-security mac-address forbidden 
معناه ان البورت ده ماتقبلش منه اى داتا
IOU1(config-if)#switchport port-security maximum ?
<1-4097> Maximum addresses
ده لو عندى على نفس interface اكتر من جهاز متوصل ف من هنا هنححد عدد الماكت للبورت ده
IOU1(config-if)#switchport port-security violation protect
IOU1(config-if)#spanning-tree portfast               ده عشان نسرع الدنيا
IOU1(config-if)# no shut
IOU1#show port-s
IOU1#show port-security
IOU1#
IOU1#show port-security int e0/0 
كده هيوريك كل االى اللى حصل فى int دى بالتفصيل

---
POE :
1- Cisco Inline Power (ILP)
> Cisco proprietary and developed prior to IEEE 802.3af.
> Supplies maximum 6.3 watts per port.

2- IEEE 802.3af (type1)
> Supplies maximum 15.4 watts per port.
> You will need at least the 740-watt power supply.
> Mode A uses pins 1,2,3,6 to send data and power.
> Mode B uses pins 4,5,7,8 to send power.

3- IEEE 802.3at (PoE+) (type2)
> Supplies maximum 30.8 watts per port.
> You will need at least the 1480-watt power supply.

4- 802.3bt (Cisco Universal Power over Ethernet UPoE) (type3)
> Supplies maximum 60 watts per port.
> Only available on the 4500E and 3850 platforms.

5- 802.3bt (Universal Power over Ethernet UPoE) (type4)
> Supplies maximum 90-95 waus per port.

دول اللى بيعرفوك الجهاز اللى قدامك محتاج power ولا لا بروتكول  CDP و LLDP ده الاحدث

---
#### PoE Configuration:

Switch(config-if)# power inline {auto [max milli-watts] }
show power inline 

#### Troubleshooting PoE:

> Common reasons why PoE might fail:

- Port is shutdown (administratively down).
- Port is err-disabled.
- Command "power inline never" was accidently configured.
- Patch panel port is bad.
- Cable from PD to PSE is bad.
- Cable from PD to PSE is too long (must be less than 100 meters).
- Total available power budget has been consumed.

switch#debug cdp packet
Switth#debug ilpower powerman
Stack-1# show cdp neighbor detail والامر ده برضه بيعرفك محتاج بور للبورت ده ولا لا

---
vlan              اللى هوا فيها بس vlanبحيث انه يذاع على  broadcat domain معمول عشان اقلل حجم ال 

---
#### inter vlan routing :
هوا المقصود بيه vlan between routing هيشوفوا بعض ك unicast

---
router on a stick
#### Config :

IOU1(config)#int e0/0
IOU1(config-if)#switchport access vlan 10
IOU1(config-if)#int e0/1
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport access vlan 20
IOU1(config-if)#int e0/2
IOU1(config-if)#switchport trunk encapsulation dot1q
IOU1(config-if)#switchport mode trunk
هنا مش محتاجين routing لانى اكنى موصل بكابلين وفيوصلوا  direct وبنشأ 2 interface وهمى موازين لل interface المتوصله حقيى والنقل بيكون من خلال interface الحقيقية
R1(config)#int f0/0
R1(config-if)#no shut دى الحاجه الوحيدة اللى بعملها عليه دى  interface الحقيقية
R1(config-if)#exit

R1(config)#==int f0/0.10==    الوهمى  interface كده انشأت 
IOU1(config-if)#encapsulation dot1q  10           vlan لازم اعمل دى الاول عشان يشتغل واديله رقم 
R1(config-subif)#ip add 192.168.10.1 255.255.255.0
R1(config)#==int f0/0.20==   
IOU1(config-if)#encapsulation dot1q  20  native        
R1(config-subif)#ip add 192.168.20.1 255.255.255.0
IOU1(config)#int f0/2
I==OU1==(config-if)#switchport trunk native vlan 10
==R1==(config-subif)#encapsulation dot1Q 10 native

![[Pasted image 20260616184343.png|458]]
MLS :

IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport access vlan 10
IOU1(config-if)#int e0/1
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport access vlan 20
IOU1(config-if)#exit
IOU1(config-if)#
IOU1(config-if)#int vlan 0
IOU1(config-if)#ip add 192.168.10.1 255.255.255.0
IOU1(config-if)#no shut
IOU1(config-if)#exit
IOU1(config-if)#int vlan 20
IOU1(config-if)#ip add 192.168.10.1 255.255.255.0
IOU1(config-if)#ip add 192.168.20.1 255.255.255.0
IOU1(config-if)#no shut
IOU1(config)#ip routing
IOU1(config)#show ip route

---
#### Ether channel 
فايدته توزع الحمل على 4 interfacee   
![[Screenshot 2026-06-16 194901 1.png|470]]
-src dst mac                    يعنى المتشابه ب صفر والمختلف ب 1 XOR ده بيشتغل ازاى ب بوابه 

etherchannel modes:
- on انى ادخله افعله ب ايدى على 2 سوتش ,static ده  dynamic الباقى 
- desirable       >PAgP (port aggregation protocol) cisco
- auto                 PAgP ده شغال بنفس البرتكول اللى فوق 

- active         >  LACP (link aggregation control protocol) std
- passive            LACP
وبيشتغلوا auto و, active , active passive,active desirable ,desirable,  desirable 
حاول على قد ما تقدر كل config تبقى زى بعضها فى السوتشين
IOU1(config)#int range e0/0 - 1
IOU1(config-if-range)#channel-group 1 mode desirable
IOU1#show etherchannel summary
IOU2#show interface status 
IOU1#show interface status err-disabled ده هيعرفك سبب المشكله اى
لو حاله interface كانت errdisable  وانا صلحتها مثلا لازم اعم كده عشان يشتغل تانى
IOU2(config)#errdisable recovery interval 30
IOU2(config)#errdisable recovery cause ==channel-misconfig==

IOU1(config)#default interface range f0/1-3                   default على  interface ده لو انتا عايز ترجع كذا 
ده بدل ما اعد امسح فى كل الاوامر اللى كتبتها
IOU1#show int e0/0 switchport                                          ده عشان تعرف حاله البورت اى

---
VTP
VLAN ==trunking== protocol
اهميته انه يعرف كل السويتشات ب كل vlans يعنى بدل ما تعمل نفس الـ VLAN على كل Switch يدويًا، تعملها مرة واحدة على Switch واحد، والباقي يتعلمها
![[Pasted image 20260617185457.png|453]]
revision time :                 عشان احل الشكلة دى اعمل اى حاجه من اللى تحت لازم ,revision time  يبقى ب 0
بحتاج اعملها ليه عشان لو عايز ارجع سويتش قديم للشبكه تانى 
- delete vlan.dat
- transparent                وده برضه لو عايز الغى البروتكول وبيقولك يعتبر شفاف بيقرا الرسايل بس
- domain name
```
VTP = توزيع VLANs بين Cisco Switches

Server:
- ينشئ VLAN 
- يرسل التحديثات
- used by defualt in switch
  
Client:
- يستقبل VLAN فقط

Transparent:
-بس لا يشارك في قاعدة  VLAN ينشأ
- يمرر رسائل VTP
- نفسه update ومش ي not Synchronize يعنى 
لازم:
Trunk + نفس Domain + نفس Password
```
config :
IOU1(config)#int e0/0
IOU1(config-if)#switchport trunk encapsulation dot1q
IOU1(config-if)#switchport mode trunk   دول اول حاجه لازم اعملها
IOU1#show vtp status
IOU1#show vlan b

![[Pasted image 20260617202247.png|357]]
## SW1 (Server)

```
Switch(config)# vtp domain CCNA
Switch(config)# vtp password 12345
Switch(config)# vtp mode server

Switch(config)# vlan 10
Switch(config-vlan)# name SALES

Switch(config)# vlan 20
Switch(config-vlan)# name IT
```
## SW2 (Client)
```
Switch(config)# vtp domain CCNA
Switch(config)# vtp password 12345
Switch(config)# vtp mode client
Switch#show vtp password
```
## for transparent
```
SW2(config)# vtp mode transparent
```

## VTP Pruning
				  Trunk وظيفته:تقليل الترافيك غير الضروري على روابط الـ 

IOU1(config)#vtp pruning                         on switch server
IOU1#show interfaces pruning
IOU1#vtp primary vlan           vlans ده عشان يعرفك انهى السيرفر الرئيسى اللى هيبقى من خلاله بس انشأ
وده من مميزات vtp pruning 

vtp version 3 ده يعتبر احسن واحد فيهمو الاتنين التانين شبه بعض بالضبط


IOU1#vtp primary vlan force          غصب primary كده ده هيبقى ال 
IOU1(config)#vtp password 123 hidden             in version 3
IOU2(config)#vtp mode ?
client	            Set the device to client mode.
off	                Set the device to off mode.
server	            Set the device to server mode.
transparent   	Set the device to transparent mode.

IOU2(config)#int e0/0
IOU2(config-if)#no vt


IOU1(config)#int e0/2
IOU1(config-if)#no swi
IOU1(config-if)#no switchport                   routed interface  الى  switched interface ده هيحولها من 

---
- FHRP(first hop redundancy protocol)
- HSRP(hot standby router protocol)
- VRRP (virtual router redundancy protocol)
- GLBP (gateway load balance protocol)
البروتكولات دى عشان لو عندى اكتر من راوتر ممكن اطلع من خلاله على النت

---
use HSRP CISCO
هنا عايز اشغل الراواتر ك جهاز كمبيوتر                                     pc(config)#ip default-gateway 192.168.1.3
pc(config)#no ip routing                                  لازم اعمل الامر ده عشان يشتغل ك جهاز
pc(config)#end

R1(config-if)#standby 1 ip 192.168.1.3                     , لراوتر وهمى  int ده اكنى عامل  virtual ده  ip
و الرقم 1 لازم يبقى واحد بين الراوترين  والراوتر كده بقى active 
==R2(==config-if)#standby 1 ip 192.168.1.3             عادى  ip  وهنا ممكن ما اكتبش   standby والراوتر ده بقى 
RI#show standby                      بقت اى int عشان اعرف حالته 
RI#show standby  brief

R1(config-if)#standby 1 priority 150
R1(config-if)#standby 1 preempt     زى ماكان active ده فى حاله لو الراوتر وقع وعايزه لما يقوم يبقى هوا 


R1(config-if)#int loop 0
R1(config-if)#ip add 1.1.1.1 255.255.255.255
R1(config-if)#
R1(config)#track 200 interface loop 0 line-protocol          interface ده هيجبلك انتا بتراقب اى
R1(config-track)#int f0/0
R1(config-if)#standby 1 track 200 decrement 60       اعلى Priority أنقص وهوا بياخد  Priority بمقدار 60
بنعمل كده ليه عشان لو الراوتر اللى شغال حصل فيه faulier ف راوتر 2 يشتغل مكانه علطول
track على موقع شغال علطول زى جوجل  ping ده مهم ممكن من خلاله اراقب اتصالى مثلا بالنت من خلال 
ممكن اراقب  R1(config)#track 300 ip route 0.0.0.0 0.0.0.0 reachability                         as         routing

show track
==R2==(config-line)#int f0/0
R2(config-if)#standby 1 preempt

R1(config-if)#standby 1 authentication md5 key-string asdwe    مهم اطبقه عشان لو حد جه يعمل اتاك

R2#debug standby terse
ممكن اعمل حركه حلوة انى اخلى 2 راوتر active بس ازاى يعنى مثلا sw1,sw2 for r1 و sw3,sw4 for r2 واخلى كل واحد منهم standby للتانى يعنى r1  standby for sw3,sw4  والتانى العكس

---
==ip in switch    بيتحط  uder vlan  as svi== 

---
- VRRP (std)   اسرع
virtual router redundancy protocol
ده فيه راوتر واحد بس master والباقى backup
R1(config)#int f0/2
R1(config-if)#no shut
R1(config-if)#vrrp 1 ip 192.168.1.3
R1(config-if)#show vrrp
يعتبر نفس ال config بتاعت HSRP   بالضبط 
R1(config-if)#vrrp 1 ?

info :      علطول بيبقى ابطأ application layer اى بروتكول شغال فى 
info 2:    خاص بيه بيبقى اسرع port number protocol اى بروتكول ليه 

---
GLBP (gateway load balance protocol)

R4(config-if)#glbp 1 ip 192.168.1.254
R4(config-if)#line vty 0 4                         telnet ده عشان ال     
R4(config-line)#no login
R4(config-line)#do show glbp brief

---
AAA
authentication التحقق
authorization  الصلاحيات
accounting   مراقبة بيراقب اليوزر بيعمل اى

R1(config)#aaa new-model
R1(config)#aaa ?
R1(config)#aaa new-model
R1(config)#aaa authentication login
R1(config)#aaa authentication login CONSOLE group ?
R1(config)#aaa authentication login CONSOLE group tacacst +
 tacacst +                   vm ده نوع السيرفر اللى هنشتغل عليه فى 
R1(config)#aaa authentication login CONSOLE group tacacst + ?
R1(config)#aaa authentication login CONSOLE group tacacst + local 
  local ده كده بقوله انا هنشأ داتا بيز على السيرفر فيها كل الاكونتات مثلا وانا هعمل على الاقل اكونت
  عشان لو حاجه وقعت او السيرفر وقع اقدر اصلح الدنيا 
  R1(config)#tacacs-server host 192.168.2.2 key 1234

---

|                 | ethernet               | wifi            |
| --------------- | ---------------------- | --------------- |
| IEEE std<br>    | 802.3                  | 802.11<br>      |
| media<br>       | copper, glass          | air<br>         |
| data format<br> | electricity, light<br> | radio waves<br> |
Wi-Fi Topologies:

AD-HOC: No AP (access point)
Infrastructure (BSS): Only one AP
Mesh (ESS): More than AP.

Wi-Fi Standards:
ex:
اللى تحت دول الشائع عندنا فى مصر فى حاجات اعلى بكتير زى (802.11) ah,af,ax,ay,ay  

| IEEE Standard<br> | Year Adopted<br> | Frequency<br> | Max. Data Rate<br> | Max. Range<br> | comman       |
| ----------------- | ---------------- | ------------- | ------------------ | -------------- | ------------ |
| 802.11b<br>       | 1999<br>         | 2.4 GHz<br>   | 11 Mbps<br>        | 450 ft.<br>    | in my labtop |
| 802.11n<br>       | 2009<br>         | 2.4/5 GHz<br> | 600 Mbps<br>       | 825 ft.<br>    | in eg        |
| 802.11ac<br>      | 2014<br>         | 5 GHz<br>     | 1 Gbps<br>         | 1,000 ft.<br>  | in eg        |

Wi-Fi Terminology:

BSS (Basic Service Set): All the parameters that the user has to agree on with the AP.
ESS (Extended Service Set): Multiple APs connected together to extend the coverage area.
BSA (Basic Set Area): The AP coverage area.
SSID (Service Set Identifier): The name of the wireless network and it is not unique.
BSSID: The unique name for the AP which runs behind the scene and it is transparent for the
user.
Distribution Network: Connecting the AP to a wired switch.
Mesh Network: Connecting more than one AP without the need for a wired system.
Roaming: A station roams from one AP to another.

Wi-Fi Components:
- Station (client)
- Access Point.
- Controller

Types of Access Points:

- Autonomous AP (Standalone AP)
- Light weight AP: controlled through a controller.

Types of Controllers:

Appliance.

---
WAN technology (public, private)

- circuit switching (leased line) ده وظيفته انى يمشى الداتا فى مسار خاص بيك انتا بس
- on demand circuit switching ده بتاجره مده بس بسيطة مش كبيرة
	>dial up
	  SDN

- packet switching

> 	X.25
	error correction / reliable
	45 Kbps

> 	frame relay
	45 Mbps

كابلات السريال مابتتعاملش ب mac
![[Pasted image 20260623103628.png|510]]
must between 2 switch use the same DLCI as 201 ,201

at startup
> neighbor discovery
   LMI inquery

interface :
- active
- inactive
- deleted


- label switching
==MPLS== (multi protocol label switching)
- lable packet  ل  ip packetوده سرعت النقل يجى 10 اضعاف      , بيقولك هنا قدروا يحولوا , 

- at startup
> neighbor discovery (LDP/ label discovery protocol)        mulicast يبعت ك 
   656 UDP 224.0.0.2
   label exchange (LDP / TCP)


هنا يبنشأ جدول اسمه LFIB وجواه رقم ل label وint زى s1
![[Screenshot 2026-06-23 195427.png|494]]

---
![[Pasted image 20260624175907.png|416]]

---
### VPN 

- confidentiality  السريه وتشمل تشفير الداتا
	1. symmetric public key,private key دى ليها 
	2. asymmetric بس هنا التشفير يبقى انك تاخد البابلك بتاع الطرف التانى   key ودى برضه ليها نفس
 تشفربه وهوا يفك التشفير بال برايفت بتاعه والعكس
- authentication
	1. pre shared key
	2. signature (CA)
- integrity
	1. MD5  128 bit
	2. SHA    256 bit
- anti replay

VPN modes:
- transport mode
- tunnel mode .

how to create the tunnel:

- GRE (generic routing encapsulation)
- PPTP (point to point tunneling protocol)
- L2TP (L2 tunneling protocol)
- IPsec
- SSTP (secure socket tunneling protocol)  use SSL/HTTPS
- IKEv2 (internet key exchange)

VPN port numbers

- PPTP: TCP 1723
- L2TP over IPsec: UDP 1701, UDP 500 and UDP 4500
- SSTP: TCP 443
- GRE: Protocol group 47

---
RADIUS
1812 for authentication
1813 for accounting

---
VPN config:
- site to site VPN   ده انى اربط فرعين ببعض كده يعنى واللى فات كله كان ده 
- remote access VPN
- SSL VPN

GRE config :   كبير على البروسوسر load عيبه انه بيعمل 
اول حاجه عملها interface وهمى عشان امشى فيه الترافك جوا ال tunnel 
HQ(config)#int tunnel 1
HQ(config-if)#ip add 192.168.1.1 255.255.255.252 يفضل يبقى برايفت

HQ(config-if)#tunnel source f0/0
HQ(config-if)#tunnel destination 11.1.0.2     اللى هناك اى interface هنا عشان انا معرفش اسم  ip كتبت 

HQ(config-if)#ip route 0.0.0.0 0.0.0.0 11.0.0.1                 defualt route هنا هقيم 

in router 2
Branch(config-if)#ip route 0.0.0.0 0.0.0.0 11.1.0.1
Branch(config)#int tunnel 1
Branch(config-if)#ip add 192.168.1.2 255.255.255.252
Branch(config-if)#tunnel source f1/0
Branch(config-if)#tunnel destination 11.0.0.2

HQ(config)#do show ip int b  وهمى ظهر interface هتلاقى 
HQ(config)#do show ip route
كده اللى ناقص ان اقيم routing table بتاعى واى  حاجه عادى
HQ(config)#int tunnel 1
HQ(config-if)#ip ospf 1 area 0
HQ(config-if)#int loo 1
HQ(config-if)#ip ospf 1 area 0
HQ#show ip ospf neighbor
ونفس الكلام على الراوتر التانى
Branch(config)#int tunnel 1
Branch(config-if)#tunnel mode ipi
Branch(config-if)#tunnel mode ipip ده نوع اخف شويه من العادى

---
IPsec       unicast لوحده بيشتغل 
GRE/IPsec :   لكن هنا بيشغل كل حاجه

GRE/IPsec config :
هنا بعد ما قومنا GRE هنضيف عليه بقى IPsec
HQ(config)#crypto isakmp policy 10
HQ(config-isakmp)#authentication pre-share
HQ(config-isakmp)#encryption aes ? ده نوع التشفير اى   
<cr>defaultعادى وهيستخدم ال enter  معانها انى ااضغط 
HQ(config-isakmp)#group ?
HQ(config-isakmp)#group 5           key ده حجم ال 
HQ(config-isakmp)#hash sha512

HQ(config)#crypto isakmp key CCNA ?
HQ(config)#crypto isakmp key CCNA address 11.1.0.2    + public ip for الطرف التانى اللى عايز اوصله

HQ(config)#do show run | s crypto  كده هيجبلك كل الاوامر اللى كتبتها

---
اللى تحت دول مراحل ال config كلها وهتطبق  على الطرف التانى بس بتغير بس فى حته ip بس
phase 1: ISAKMP

crypto isakmp policy 10
encr aes
hash sha512
authentication pre-share
group 5
exit
crypto isakmp key CCNA address 11.0.0.2
ip access-list extended LIST
permit gre host 11.1.0.2 host 11.0.0.2

phase2: IPsec

crypto ipsec transform-set

crypto map:
SECRET esp-aes esp-sha512-hmac
crypto map GRE-IPSEC 10 ipsec-isakmp
set peer 11.1.0.2
set transform-set SECRET
match address LIST

int f0/0                           interface هنا  لازم اطبق كل اللى عملته فوق على 
crypto map GRE-IPSEC

HQ#show crypto isakmp sa             ;كده هيورك ان التنل شغاله ولا لا
HQ#show crypto ipsec sa  

![[Screenshot 2026-06-24 213410.png]]

---
DMVPN : دى عشان لو عندى فروع كتير وعايز اربطهم ببعض

---
IPv6 migration : عشان هيا بتشيل اى حاجه فيه GRE tunnle وادخله فى  ipv6  lan  ده انى اشغل فى منطقة 

---
syslog

- console
- telnet
- buffer
التلاته اللى فوق اللوجز اللى فيهم متطايرة 
- flash
- syslog server
- SNMP

R1(config)#logging buffered 5   الحفظ فى البافر
R1(config)#clear logging   كده حذفته
R1(config)#show logging    
R1(config)#logging persistent url disk0    disk0 اكنه مكان فلاش flash كده انتا بتقوله احفظلى اللوج فى 

Kiwi Syslog Daemon  ده برنامج على الجهاز بحمله بشغله ك سيرفر ل للوجز
R1(config)#logging host 192.168.1.200  ده بتاع جهازى ipكده بقوله ارمى اللوجز على السيرفر اللى واخد ال
R1(config)#logging origin-id ?
R1(config)#logging origin-id hostname كده هيظهر اسم الراوتر فى الوج الى هتجيلك

---
SNMP
 Cacti                       SNMPده البرنامج اللى هثبته عندى واشغله ك سيرفر ل 
 http://localhost/cacti                عليه access    عشان  browser واكتب كده فى ال 
 
R1(config)#snmp-server community CISCO ro
اللى تحت دول اختيارى بس كويسين 
R1(config)#snmp-server location "HQ Office"
R1(config)#snmp-server contact abeer@cisco.com

![[Pasted image 20260625213851.png]]

in version 3
Configuration

R1(config)#snmp-server group IT v3 priv
R1(config)#snmp-server user abeer IT v3 auth sha CISCO priv aes 256 CISCO
R1#show snmp user

