Proyecto aplicado:
Reconstrucción y regularización en imágenes tomográficas
MétodosComputacionales,S22026
1. Introducción
Unatomografíacomputadapermiteobservarelinteriordeunobjetosinabrirloapartirdemedicionesob-
tenidasmedianterayosX.Enelmodelosimplificadoqueutilizaremosenestetrabajo,unemisorcolimado
generaunafamiliaderayosparalelosqueatraviesanunaseccióndelobjetoysonregistradosporunarre-
glolinealdedetectoresubicadodelladoopuesto.Cadadetectormidecuántaintensidadlograatravesarel
objetoalolargodeunodeesosrayos.Losmaterialesconmayorcoeficientedeatenuaciónreducenmásla
intensidad del haz, mientras que aquellos con menor coeficiente de atenuación permiten que llegue una
intensidadmayoraldetector.
ComoseilustraenlaFigura1,paraunaorientacióndada?seadquiereunconjuntodemedicionescorres-
pondientesarayosparalelosqueatraviesandistintasregionesdeunaseccióndecorte.Alcambiarelángulo
?,elsistemaobservaelmismoobjetodesdeunanuevadirección.Repitiendoesteprocedimientoparamúlti-
plesorientacionesseobtienelainformaciónnecesariaparaintentarreconstruirladistribucióndeatenuación
enelinteriordelasección.
?
detectores
emisorcolimado
seccióndelobjeto
(a)Ejemplodeseccióntomográfica. (b)Modelodeadquisiciónconrayosparalelos.
Figura1:Unatomografíapermitereconstruirunaseccióndelobjetoapartirdemedicionestomadasdesdemúltiples
orientaciones.Alaizquierdasemuestran4imágenestomográficasdedistintoscortesdelcerebrohumano;aladerecha,
elmodelosimplificadodeadquisiciónqueutilizaremosenestetrabajo.
Si I es la intensidad emitida por uno de los rayos de la Figura 1 e I es la intensidad registrada por el
0
detectorcorrespondientedespuésdeatravesarelobjeto,unmodelosimplificadorelacionaambascantidades
mediante
(cid:18) (cid:90) (cid:19)
I =I exp ? µ(r)dr .
0
?
Aquí,µ(r)describelaatenuacióndelmaterialencadapuntoy?representaelrecorridodelrayoatravésdel
1

objeto.Altomarlogaritmosseobtieneunamediciónlinealenlaatenuación:
(cid:18) I (cid:19) (cid:90)
?log = µ(r)dr.
I
0 ?
Porlotanto,cadadetectorentregaesencialmenteunasumadelasatenuacionesencontradasalolargodel
recorridodeunodelosrayos.
Enestetrabajoprácticoestudiaremosunaversióndiscretaysimplificadadelproblemadereconstrucciónde
unaimagentomográfica.Representaremosunaseccióndelobjetomedianteunaimagenpequeña,dividida
enpíxeles.Cadarayoproduciráunamediciónasociadaalaatenuaciónacumuladaalolargodesurecorrido
yelconjuntocompletodemedicionespodráescribirsecomounsistemalinealdelaforma
b=Ax.
Elvectorxrepresentarálaimagendesconocida,cadafiladeAdescribirácómounrayoatraviesalosdistintos
píxelesybcontendrálasmedicionesdelescáner.
Comenzaremos simulando la adquisición de una tomografía ideal. Luego estudiaremos qué ocurre cuan-
do la cantidad de proyecciones no alcanza para determinar una imagen de manera única. Más adelante
incorporaremosruido,formularemoslareconstruccióncomounproblemademínimoscuadradoseimple-
mentaremosmétodositerativosdeoptimización.Finalmente,incorporaremosunapenalizacióndesuavidad
alproblemadereconstrucciónyestudiaremoselcompromisoentrefidelidadalasmedicionesyreducción
delruido.
2. Objetivos
Esperamosquelogren:
representarunaimagencomounvectoryunaadquisicióntomográficacomounatransformaciónli-
neal;
construireinterpretarunamatrizdeproyecciónapartirdeunconjuntoderayos;
relacionarlacantidadyorientacióndelasmedicionesconelrangoyelespacionulodeunamatriz;
formularunareconstrucciónconruidocomounproblemademínimoscuadrados;
interpretarlasolucióndemínimoscuadradoscomounaproyecciónortogonal;
reconocerelroldelamatrizsimétricaATAydesusvalorespropios;
implementargradientedescendienteyelmétododeNewtonparaminimizarfuncionescuadráticas;
construirmatricesdediferenciasparamedirlafaltadesuavidaddeunaimagen;
formular una reconstrucción regularizada mediante una penalización cuadrática de las diferencias
entrepíxelesvecinos;
analizarelcompromisoentrefidelidadalasmediciones,reducciónderuidoypreservacióndedetalles.
3. Temario
Imágenescomovectores Formascuadráticas
Transformacioneslineales Valorespropios
Sistemaslineales Gradientedescendiente
Rangoyespacionulo MétododeNewton
Mínimoscuadrados Regularización
Proyeccionesortogonales VisualizaciónenPython
Matricessimétricas
2

| 4. Modelo | de adquisición | tomográfica |     |     |     |
| --------- | -------------- | ----------- | --- | --- | --- |
Geometríadeadquisición
Enestetrabajoreconstruiremosunaúnicasecciónbidimensionaldelobjeto,comouncortevistodesdearriba.
Laimagendesconocidaserepresentarámedianteunafuncióndeatenuaciónµ(u,v)definidasobreeseplano.
UnvaloraltodeµrepresentaunaregiónqueatenúafuertementelosrayosX;unvalorbajorepresentauna
regiónmástransparente.
Enuntomógraforeal,elpacientepermaneceaproximadamentefijomientraselsistemadeadquisicióngiraa
sualrededoryregistramedicionesdesdemúltiplesorientaciones.Encadaorientación,distintosrayosatra-
viesanelmismoplanodecorteyproducenunconjuntodemediciones.Desdeelpuntodevistamatemático,
rotarelsistemadeadquisiciónalrededordelobjetoesequivalenteamantenerfijaladireccióndelosrayos
yrotarelobjetoensentidocontrario(comoseesquematizaenlaFigura1).
Los tomógrafos modernos, además, utilizan geometrías de adquisición más complejas que la que consi-
deraremosenestetrabajo.Enlatomografíacomputadaconvencionaleshabitualunageometríadehazen
abanico,mientrasqueotrossistemas,comolosdecone-beamCT,utilizanunhazcónico.Nosotrosadoptare-
mosunaidealizaciónmássencilladenominadageometríadehazparalelo,queconservalaestructuraesencial
delproblemadereconstrucciónypermiteconcentrarnosenlosaspectosalgebraicosynuméricos.
Parametrizacióndelosrayos
| Representaremoscadapuntodelplanomedianteelvector |     |     |     | v   |     |
| ------------------------------------------------ | --- | --- | --- | --- | --- |
(cid:18) u (cid:19)
|                                                     |     | ?R2. |     |     | ?   |
| --------------------------------------------------- | --- | ---- | --- | --- | --- |
|                                                     | p=  |      |     |     | ?   |
|                                                     |     | v    |     |     | ,s  |
| Paraunaorientación?,definimoselvectornormalunitario |     |      |     | p   | n   |
?
|     |     | (cid:18) (cid:19) |     |     | s   |
| --- | --- | ----------------- | --- | --- | --- |
cos?
|     | n   | = . |     |     | ?   |
| --- | --- | --- | --- | --- | --- |
?
sin? u
Laecuacióncentraldeestaparametrizaciónes
|     | nTp=ucos?+vsin? | =s. |     |     |     |
| --- | --------------- | --- | --- | --- | --- |
?
Figura2:Todopuntopdelrayodesta-
cadotienelamismaproyecciónssobre
| Elproductointernoeslaproyeccióndepsobren |               |                  | .Alfijarlaens(ver |           |     |
| ---------------------------------------- | ------------- | ---------------- | ----------------- | --------- | --- |
|                                          |               |                  | ?                 | lanormaln | ?.  |
| Figura 2), estamos                       | seleccionando | todos los puntos | de una recta      | per-      |     |
| pendicularan                             | ? :           |                  |                   |           |     |
|                                          |               | (cid:8)          |                   | (cid:9)   |     |
|                                          |               | ? =              | p?R2 :nTp=s       | .         |     |
|                                          |               | ?,s              | ?                 |           |     |
Por lo tanto, ? fija la orientación de una familia de rayos paralelos y s selecciona uno de ellos. Como n ?
tienenormauno,sesademásladistanciaconsignoentreelrayoyelorigen;enelmodelodeadquisición,
cada valor de s corresponde a una posición del detector. Es importante notar que el ángulo ? describe la
orientacióndelanormal,noladireccióndepropagacióndelrayo.Encadaadquisiciónsefija?yserecorren
distintasposicioness.Luegosecambiaelánguloyserepiteelproceso.Como?y?+?generanlasmismas
rectas,bastaconsiderar
? ?[0,?).
|     |     | ? = | 0 ? = ?/2 |     |     |
| --- | --- | --- | --------- | --- | --- |
A modo de ejemplo, se ilustran los casos y que permiten verificar otras configuraciones
del detector (ver Figura 3). Representaremos cada rayo mediante la recta completa ? . Sin embargo, la
?,s
mediciónacumulaatenuaciónúnicamenteenlapartedelrayoqueatraviesaelobjeto;fueradeesaregión
consideramosqueµ(u,v)=0.
3

|     | v   |     |     | v   |     |
| --- | --- | --- | --- | --- | --- |
n ?/2
|     | ? =0 |     |     | s ? =?/2 |     |
| --- | ---- | --- | --- | -------- | --- |
s n
0
|     |     | u   |     | u   |     |
| --- | --- | --- | --- | --- | --- |
(a)Caso?=0:laecuaciónesu=sylosrayosson (b)Caso?=?/2:laecuaciónesv=sylosrayosson
|     | verticales. |     |     | horizontales. |     |
| --- | ----------- | --- | --- | ------------- | --- |
Figura3:Doscasosparticulares.Elángulo?describelaorientacióndelanormal:para? =0losrayossonverticalesy
para?=?/2sonhorizontales.
Lasmedicionesyelsinograma
Cadadetectorregistralaatenuaciónacumuladaalolargodeunrayo.Paraunángulofijo?,elconjuntode
medicionesobtenidoenlasdistintasposicionesssedenominaproyección.
SiutilizamosK ángulosyqposicionesdedetector,organizaremostodaslasmedicionesenunamatriz
|     |     | S ?RK×q, |     |     |     |
| --- | --- | -------- | --- | --- | --- |
denominadasinograma.CadafiladeS correspondeaunángulodeadquisiciónycadacolumna,aunapo-
sicióndeldetector.
LamatrizSpuedevisualizarsecomounaimagen,utilizan-
?
doelvalordecadaentradaparadeterminarlaintensidad
?
| del píxel correspondiente, | como se         | ilustra en la Figura | 4.  |     |     |
| -------------------------- | --------------- | -------------------- | --- | --- | --- |
| En esta representación     | aparecen trazas | curvas: al cambiar   |     |     |     |
?
| elángulo?,unmismopuntodelobjetocontribuyeendis- |     |     |     | ?/2 | k   |
| ----------------------------------------------- | --- | --- | --- | --- | --- |
tintasposicionessdeldetector.Estavariaciónessinusoidal,
deallíelnombresinograma.
0
s
Para formular posteriormente el sistema lineal, recorrere- s s
|               |                           |     |       | m´?n 0 | ma´x |
| ------------- | ------------------------- | --- | ----- | ------ | ---- |
|               | S                         | Kq  |       |        |      |
| mos la matriz | por filas y almacenaremos | sus | medi- |        |      |
cionesenunúnicovector Figura 4: Sinograma. La fila resaltada contiene la
|     |             |     | proyeccióncorrespondientealángulo? |     | k.  |
| --- | ----------- | --- | ---------------------------------- | --- | --- |
|     | b?Rm, m=Kq. |     |                                    |     |     |
Así,lasprimerasqcomponentesdebcorrespondenalasmedicionesparaelprimerángulo,lassiguientesq
alsegundo,yasísucesivamente.
Discretizaciónmatricial
Representaremoslaimagentomográficareconstruidamedianteunamatriz
?Rn×n,
X
dondecadaentradacontienelaatenuacióncorrespondienteaunpíxel.Paratrabajarconálgebralineal,api-
laremossusfilasenunvector
x?Rn2
.
EnPython,ambasrepresentacionesserelacionanmediante
4

x = X.reshape(-1)
X = x.reshape(n, n)
UnaadquisiciónconK ángulosyqposicionesdedetectorproduce
m=Kq
mediciones,quealmacenaremosenelvectorb?Rm.
LarelaciónentrelaimagenX yestasmedicionesserepresentamedianteunamatriz
A?Rm×n2
.
CadafiladeAcorrespondeaunrayoycadacolumna,aunpíxel.Laentradaa indicalalongituddeltramo
rj
delrayorqueatraviesaelpíxelj;sinoloatraviesa,entoncesa =0(verFigura5).
rj
rayor
0 1 2
filardeA
3 4 5
0 0 ar2 ar3 ar4 ar5 ar6 0 0
j=0 j=1 j=2 j=3 j=4 j=5 j=6 j=7 j=8
6 7 8
píxelesdeX
Figura 5: Relación entre un rayo y una fila de A. Los píxeles atravesados producen entradas no nulas; los restantes
producenceros.
Porlotanto,lamediciónproducidaporelrayores
n2?1
(cid:88)
b = a x =(Ax) .
r rj j r
j=0
Alreunirtodaslasmedicionesobtenemoselsistemalineal
b=Ax .
Como cada rayo atraviesa solamente algunos píxeles, la mayoría de las entradas de A son cero: A es una
matriz dispersa o rala. Por ejemplo, para una imagen de 32 × 32 píxeles, 60 ángulos y 46 posiciones de
detector,
n2 =1024, m=60·46=2760,
porloque
A?R2760×1024, x?R1024, b?R2760.
Unprimermodelobinario
Antesdetrabajarconlaslongitudesconlasquecadarayoatraviesalospíxeles,construiremosunaversión
binaria más sencilla de la matriz A. En este primer modelo, los píxeles con los que contribuye un rayo se
seleccionanúnicamenteapartirdesuscentros.
Identificamoscadapíxelmediantesusíndices(i,j).Dadostresenteros?,?ys,diremosqueelcentrodeun
píxelpertenecealrayodiscretocuando
?i+?j =s.
5

Elpar(?,?)determinalaorientacióndelafamiliaysseleccionaunodesusrayos.Estareglaimitalaecuación
continuanTp=s,peronocalculalainterseccióngeométricaentrelarectaycadapíxel.Porlotanto,larecta
?
dereferenciapuedeatravesarpíxelescuyoscentrosnosatisfacenlaecuación(verFigura6,derecha).Eneste
modeloinicialesospíxelesnoseránseleccionados.
|     |     | r, aT A |     |     |     |     |     |
| --- | --- | ------- | --- | --- | --- | --- | --- |
Para cada rayo discreto la fila r de contiene un 1 r1: i+j=3 r2: 2i+j=4
enlasposicionesdelospíxelesseleccionadosyun0en j=0j=1j=2j=3 j=0j=1j=2j=3
| lasrestantes,comomuestralaFigura6.Porlotanto, |     |     |     | i=0 | i=0 |     |     |
| --------------------------------------------- | --- | --- | --- | --- | --- | --- | --- |
|                                               |     |     |     | i=1 | i=1 |     |     |
=aTx
b r r
|                                                |     |     |     | i=2 | i=2 |     |     |
| ---------------------------------------------- | --- | --- | --- | --- | --- | --- | --- |
| eslasumadelasintensidadesdelospíxelescuyoscen- |     |     |     | i=3 | i=3 |     |     |
trospertenecenalrayodiscreto.
aT
r1 =[0001|0010|0100|1000]
aT
r2 =[0000|0010|1000|0000]
| Esta primera | versión de | la matriz A codifica | solamen- |     |     |     |     |
| ------------ | ---------- | -------------------- | -------- | --- | --- | --- | --- |
telapertenenciadeloscentrosalosrayosdiscretos.En
Figura6:Dosrayosdiscretoscondistintasorientacio-
elmodelofinal,todopíxelgeométricamenteatravesado
nes.Lasrectasdiscontinuasmuestranladireccióngeo-
contribuiráalamedición.Losunossereemplazaránpor
|     |     |     |     | métrica de referencia, | pero sólo | se seleccionan | los pí- |
| --- | --- | --- | --- | ---------------------- | --------- | -------------- | ------- |
laslongitudesdeintersección,loquepermitiráponde-
xelescuyoscentrossatisfacenlaecuaciónindicada.El
rarlacontribucióndelrayoacadapíxelsegúnladistan- valorseslaconstantedeesaecuaciónynonecesaria-
| ciaquerecorreensuinterior. |     |     |     | mentecoincideconunafilaocolumna. |     |     |     |
| -------------------------- | --- | --- | --- | -------------------------------- | --- | --- | --- |
(1,0)
| Usaremos | las direcciones | para seleccionar | filas, |     |     |     |     |
| -------- | --------------- | ---------------- | ------ | --- | --- | --- | --- |
(0,1)paraseleccionarcolumnas,(1,1)y(1,?1)paralasdosfamiliasdediagonales,y(2,1)paraincorporar
rayosconotrainclinación,entreotras.
| 5. Uso | de IA generativa |     |     |     |     |     |     |
| ------ | ---------------- | --- | --- | --- | --- | --- | --- |
ElusodeherramientasdeIAgenerativaestápermitidoenesteproyectoaplicado,siemprequeseaunsoporte
paraelaprendizajeynounreemplazodeltrabajopropio.Todaasistenciadeberáestardocumentadaenla
entrega(uso_ia.txt):quéherramientaseusóyparaquéseusó.
| Categoría |     | Descripción |     |     | Estado |     |     |
| --------- | --- | ----------- | --- | --- | ------ | --- | --- |
Hacertodoeltrabajopor UsarIAparacompletartodoslosejerciciosporqueno PROHIBIDO.Hablá
mí teníatiempo,estabacansadoonoleveíasentidoal coneldocenteparauna
|     |     | trabajo. |     |     | alternativa. |     |     |
| --- | --- | -------- | --- | --- | ------------ | --- | --- |
Hacereltrabajorepetitivo UsarIAparacompletaralgunosejerciciosqueconsidero DESALENTADO.
|     |     | repetitivosoquenoaportanaprendizaje. |     |     | Consultáconel |     |     |
| --- | --- | ------------------------------------ | --- | --- | ------------- | --- | --- |
docenteantes.
Arrancar/destrabarme UsarIApararesumirconsignas,planificar,armarun PERMITIDO.
|     |     | esquemaodestrabarmecuandoestoytrabado. |     |     | Documentásuusoen |     |     |
| --- | --- | -------------------------------------- | --- | --- | ---------------- | --- | --- |
laentrega.
Pedirfeedback UsarIApararevisarmirazonamiento,detectarerroreso PERMITIDO.
|     |     | recibirsugerenciassobremitrabajo. |     |     | Documentásuusoen |     |     |
| --- | --- | --------------------------------- | --- | --- | ---------------- | --- | --- |
laentrega.
Ayudarmeaaprender UsarIAparaexplicarconceptosnuevosodifícilespasoa FOMENTADO.
|     |     | paso,conejemplosoanalogías. |     |     | Mencionásuusoenla |     |     |
| --- | --- | --------------------------- | --- | --- | ----------------- | --- | --- |
entrega.
Amplificarmitrabajo UsarIAparairmásalládeloquepidelaconsigna, FOMENTADO.
|     |     | explorarextensionesoprofundizar. |     |     | Documentásuusoen |     |     |
| --- | --- | -------------------------------- | --- | --- | ---------------- | --- | --- |
laentrega.
6

6. Condiciones de entrega y modalidad de aprobación
Laentregadeberárealizarseenunúnicoarchivocomprimidoenformato.zip.Elarchivodeberácontener
unnotebookdeJupyterporejercicio(yaejecutado).Losnotebooksdebenincluirelcódigo,lasexplicaciones,
losgráficossolicitadosylasrespuestasconceptuales.
Tambiénsedeberáentregarunapresentaciónenformato.pdfqueresumalassolucionesdeltrabajopráctico.
Estapresentaciónpodráutilizarsecomomaterialdeapoyodurantelainstanciaoral.
Además,deberánincluirunarchivouso_ia.txtenelqueindiquensiutilizaronherramientasdeIAgene-
rativay,encasoafirmativo,enquéejerciciosyconquépropósito.
Laestructuraesperadadelaentregaeslasiguiente:
apellido-1_nombre-1_apellido-2_nombre-2_proyecto_aplicado.zip
|-- ejercicio_1.ipynb
|-- ejercicio_2.ipynb
|-- ...
|-- uso_ia.txt
|-- presentacion.pdf
|-- figuras/
| |-- ejercicio_XX.png
| |-- ejercicio_YY.png
| |-- ...
Los notebooks deben poder ejecutarse de principio a fin sin errores y deben ser autocontenidos. Se reco-
miendautilizarsolamentenumpy,scipyymatplotlib.Siutilizanalgunaotrabiblioteca,deberánjustificarlo
brevemente.
Fechasdeentrega
Lasentregasestaránhabilitadasdesdeelmartes15deseptiembreypodránrealizarseencualquiermomento
hastaellunes2denoviembrealas23:59.
Importante:Cadagrupopodráentregareltrabajocuandoconsiderequeestálisto.Unavezrealizadala
entrega,seleasignaráunafechaparalainstanciaoral.
Conelobjetivodereconocerlaautonomíaenlaorganizacióndelaprendizaje,laplanificacióndeltrabajoy
la dedicación sostenida desde etapas tempranas, se otorgará una bonificación sobre el puntaje del oral de
acuerdoconlafechadeentrega:
Períododeentrega Bonificación
Delmartes15allunes28deseptiembre,inclusive +15puntos
Delmartes29deseptiembreallunes12deoctubre,inclusive +10puntos
Delmartes13allunes26deoctubre,inclusive +5puntos
Delmartes27deoctubreallunes2denoviembre,inclusive Sinbonificación
Labonificaciónsóloseaplicarásilainstanciaoralseencuentraaprobadaantesdesumaresospuntos.Por
lotanto,nopodráutilizarseparaconvertirunoraldesaprobadoenunoaprobado.Elpuntajedeloral,inclu-
yendolabonificación,nopodrásuperarlos70puntos.
Evaluación
Lacalificaciónsedividiráendoscomponentes:
lapresentaciónentregadatendráunvalordehasta30puntos;
7

lainstanciaoraltendráunvalordehasta70puntos.
Enlaevaluacióndelaentregasetendráencuentalacorreccióndelassoluciones,laclaridaddelasexplica-
ciones,lacalidaddelosgráficos,laorganizacióndelcódigoylapresentacióngeneraldeltrabajo.
Durante la instancia oral deberán responder preguntas sobre los ejercicios entregados. Las preguntas po-
drán estar relacionadas con la implementación de las funciones, las decisiones tomadas en el código, la
interpretacióndelosresultadosygráficos,olaformulaciónteóricaquerespaldaalgunarespuesta.
Para aprobar el trabajo práctico será necesario aprobar la instancia oral sin considerar la bonificación por
entregaanticipada.Unaentregacorrectaoinclusoperfectanoserásuficientesisusautoresnopuedenex-
plicarcómofueresuelta,quédecisionestomaron,quéconceptosestáninvolucradosoporquélosresultados
obtenidostienensentido.
ElusodeherramientasdeIAgenerativaestápermitidodeacuerdoconlascondicionesestablecidaseneste
trabajo,peronoeximedecomprenderypoderexplicarlatotalidaddesuentrega.
Ejercicios
EncadaejercicioseindicauntagdeusodeIAgenerativa.Laetiqueta significaqueelejerciciode-
SinIA
beresolversesinasistenciadeIA.Laetiqueta Asistenciaparadestrabar/corregir significaquepuedenusarIApa-
ra destrabarse, revisar errores o pedir feedback, pero la solución debe ser propia y verificada. La etiqueta
IAnecesaria significaqueseesperaqueusenIAcomoherramientadeasistenciaparaprogramar,hacerde-
buggingoexploraralternativas,comprendiendocabalmentecadaaspectodelasolución.
1. Unaimagencomomatrizycomovector
SinIA
Utilizaremos inicialmente una imagen de 8×8 píxeles, representada mediante la matriz X fantoma ?
R8×8.Estaclasedeimagendepruebasueledenominarsefantoma:nocorrespondeaunescaneoreal,
sinoquefueconstruidaparaestudiarelalgoritmodereconstrucciónencondicionescontroladas.
CrearenPythonlasiguientematriz:
| X_fantoma = | np.array([ |           |            |     |
| ----------- | ---------- | --------- | ---------- | --- |
| [0.0, 0.0,  | 0.0, 0.0,  | 0.0, 0.0, | 0.0, 0.0], |     |
| [0.0, 0.0,  | 0.8, 0.3,  | 0.3, 0.3, | 0.0, 0.0], |     |
| [0.0, 0.8,  | 0.8, 0.8,  | 0.3, 0.3, | 0.3, 0.0], |     |
| [0.0, 0.3,  | 0.8, 0.3,  | 0.3, 0.3, | 0.3, 0.0], |     |
| [0.0, 0.3,  | 0.3, 0.3,  | 0.3, 0.5, | 0.3, 0.0], |     |
| [0.0, 0.3,  | 0.3, 0.3,  | 0.5, 0.5, | 0.5, 0.0], |     |
| [0.0, 0.0,  | 0.3, 0.3,  | 0.3, 0.5, | 0.0, 0.0], |     |
| [0.0, 0.0,  | 0.0, 0.0,  | 0.0, 0.0, | 0.0, 0.0], |     |
], dtype=float)
a) VisualizarX medianteplt.imshow,utilizandounaescaladegrisesconsureferencia.
fantoma
|     | X   | x   | ? R64 | reshape(-1), |
| --- | --- | --- | ----- | ------------ |
b) Convertir fantoma en el vector fantoma mediante que apila las filas de la
matriz.Llamaremosx alvaloralmacenadoenlaposiciónk,conk =0,...,63.
k
c) Recuperarlamatrizoriginalapartirdex yverificarnuméricamentequeambasrepresen-
fantoma
tacionescontienenlamismainformación.
d) Explicarquémagnitudrepresentaelvalordeunacomponentex enelmodelotomográfico.In-
k
terpretarquésignificanvaloresaltos,bajosynulosdeesacomponente.
e) ModificarunpíxeldeX ylocalizarquécomponentex cambia.Suponiendoquelosíndices
|     |     | fantoma |     | k   |
| --- | --- | ------- | --- | --- |
comienzanencero,expresarmedianteunaecuaciónlarelaciónentrelosíndices(i,j)delpíxely
suposiciónkenelvector.
8

2. SinIA Simulacióndeunúnicorayobinario
Utilizaremos el fantoma del ejercicio anterior para simular una medición del modelo binario. Una
direccióndiscretaestádeterminadaporelpar(?,?)yundesplazamientoenteros.Elrayocorrespon-
dienteseleccionaúnicamentelospíxelescuyoscentrossatisfacen?i+?j =s.
a) Implementarlafunciónmascara_rayo(n, direccion, s),quedevuelvaunamatrizM ?{0,1}n×n
r
conun1enlospíxelesseleccionadosporelrayoryun0enlosrestantes.
b) Visualizarlasmáscarasparalasdirecciones(1,0),(0,1),(1,1),(1,?1)y(2,1),utilizandorespec-
tivamentelosdesplazamientoss=3,5,7,0,8.
(cid:80)
c) Paracadamáscara,calcularlamediciónb = (M ) (X ) medianteunproductocom-
r i,j r ij fantoma ij
ponenteacomponenteentrelamáscarayelfantoma.
d) Explicarporquédosrayosconorientacionesdiferentespuedenproducirelmismovalordeme-
diciónaunqueseleccionendistintospíxeles.
3. SinIA Construccióndelamatrizdeproyecciónbinaria
ContinuaremosutilizandolamatrizX ysuversiónvectorizadax construidasenelEjer-
fantoma fantoma
cicio 1. El objetivo de este ejercicio es reunir las máscaras de distintos rayos en una única matriz de
proyección A. Para cada rayo r, vectorizaremos su máscara M por filas y utilizaremos el vector re-
r
sultantecomolafilaaT deA.Deestemodo,sisegeneranmrayosparaunaimagenden×npíxeles,
r
entoncesA?Rm×n2
.
a) Implementarlafunciónmatriz_proyeccion(n, direcciones).Paracadadirección(?,?),sede-
berán calcular los distintos valores de s = ?i+?j, ordenarlos de menor a mayor, construir la
máscaracorrespondienteacadaunoyagregarsuversiónvectorizadacomounafiladeA.
b) Hacerquelafuncióndevuelvatambiénunalistaconelpar((?,?),s)asociadoacadafiladeA,
respetandoelordendelasdireccionesrecibidas.
c) Aplicarlafunciónconn = 8ydirecciones = [(1, 0), (0, 1)].Verificarqueseobtienen16
rayosyqueA?R16×64.
d) Explicar qué representa una fila de A y qué representa una columna. Visualizar la matriz me-
dianteplt.imshowyexplicarporquécontieneunagrancantidaddeceros.
e) Calcularlasmedicionesb = Ax .Verificarquesusprimerasochocomponentescoinciden
fantoma
conX_fantoma.sum(axis=1)ylasochorestantesconX_fantoma.sum(axis=0).Graficarambos
bloquescomoseñalesunidimensionales.
4. Asistenciaparadestrabar/corregir ¿Esposiblereconstruirlaimagendemaneraúnica?
Continuaremos trabajando con la matriz X y el vector x del Ejercicio 1, junto con la
fantoma fantoma
funciónmatriz_proyeccionimplementadaanteriormente.Elobjetivoesinvestigarsilasmediciones
b = Axcontieneninformaciónsuficientepararecuperarunaimagende8×8demaneraúnica.Para
ello,analizaremoselrangoyelespacionulodedistintasmatricesdeproyección.
a) Considerar una matriz de proyección A ? Rm×64. Utilizando el teorema de rango y nulidad,
determinar qué condición debe cumplir rango(A) para que la imagen pueda reconstruirse de
manera única. Explicar qué ocurre si existe un vector no nulo v tal que Av = 0 y relacionar
rango(A)condim(Nul(A)).
b) Construir las matrices A , A y A utilizando D = {(1,0),(0,1)}; D = D ?{(1,1),(1,?1)};
2 4 7 2 4 2
y D = D ?{(2,1),(1,2),(3,1)}. Para cada matriz, informar sus dimensiones, su rango, la di-
7 4
mensióndesuespacionuloysipermitedeterminarunaimagendemaneraúnica.Compararla
cantidaddefilasconelrangoeidentificarlapresenciademedicionesredundantes.
c) ParaA ,utilizarunadescomposiciónSVDparaencontrarunvectornonulov ? R64 queperte-
4
nezcaaproximadamenteasuespacionuloyverificarque?A v??0.Calcularb =A x y
4 4 4 fantoma
construirx =x +?v,con? ?=0.Visualizaryverificarqueproducenaproximadamente
alt fantoma
lasmismasmedicioneseinterpretarquéinformacióndelaimagenresultainvisibleparaA .
4
9

| 5. Asistenciaparadestrabar/corregir |     |     |     | Reconstrucciónencondicionesideales |     |     |     |     |     |
| ----------------------------------- | --- | --- | --- | ---------------------------------- | --- | --- | --- | --- | --- |
Continuaremosutilizandoelvectorx delEjercicio1ylasmatricesA ,A yA construidasan-
|     |     |     |     |     | fantoma |     |     | 2 4 7 |     |
| --- | --- | --- | --- | --- | ------- | --- | --- | ----- | --- |
teriormente.Trabajaremosconmedicionesideales,sinruido,paraobservarcómoelrangodelamatriz
deproyecciónafectalareconstrucción.Enparticular,A tienerangocolumnacompleto,mientrasque
7
A yA
2 4 nodeterminanunasoluciónúnica.
a) Para cada k ? {2,4,7}, generar las mediciones b = A x y resolver el sistema median-
|     |     |     |     |     |     | k   | k fantoma |     |     |
| --- | --- | --- | --- | --- | --- | --- | --------- | --- | --- |
te np.linalg.lstsq para obtener una reconstrucción x . En los casos sin solución única, esta
(cid:98)k
funcióndevolveráunasolucióndenormamínima.
b) Para cada reconstrucción, calcular el residuo r = b ?A x y su norma. Calcular también el
|     |     |     |     |     |     | k k | k(cid:98)k |     |     |
| --- | --- | --- | --- | --- | --- | --- | ---------- | --- | --- |
errorrelativodividiendo?x ?x ?por?x ?.ResumirenunatablaelrangodeA ,la
|     |     |     |     | (cid:98)k | fantoma | fantoma |     |     | k   |
| --- | --- | --- | --- | --------- | ------- | ------- | --- | --- | --- |
normadelresiduoyelerrorrelativo.
c) Visualizar,paracadacaso,laimagenoriginal,lareconstrucciónyladiferenciaentreambas,uti-
lizandolamismaescaladecolor.Explicarporquéunresiduocercanoaceronogarantizaquela
imagenoriginalhayasidorecuperadacorrectamenteyrelacionarelresultadoconelespacionulo
|     | deA | .   |     |     |     |     |     |     |     |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
k
| 6. Asistenciaparadestrabar/corregir |     |     |     | Medicionesconruidoymínimoscuadrados |     |     |     |     |     |
| ----------------------------------- | --- | --- | --- | ----------------------------------- | --- | --- | --- | --- | --- |
Continuaremos utilizando el vector x del Ejercicio 1 y la matriz A . Las mediciones ideales
|      |     |     |     |     | fantoma |     |     | 7   |     |
| ---- | --- | --- | --- | --- | ------- | --- | --- | --- | --- |
| sonb | =   | A x |     |     |         |     |     |     |     |
7 7 fantoma .Parasimularunaadquisiciónreal,agregaremosruidoyobtendremosmedicio-
nes perturbadas b = b +? . Debido al ruido, en general no existirá una imagen que reproduzca
|     |     | ?   | 7   | ?   |     |     |     |     |     |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
exactamentetodaslasmediciones.Utilizaremosmínimoscuadradosparaencontrarlaimagencuyas
| medicionessimuladasseanlomáscercanasposibleab |     |     |     |     |     | ? . |     |     |     |
| --------------------------------------------- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
a) Fijarunasemillaaleatoriaygenerarunúnicovectorz,delmismotamañoqueb ,concomponen-
7
tesgaussianasestándar.Calcularescala = np.sqrt(np.mean(b7**2));estevalorrepresentael
tamañotípicodelasmediciones.Paracada? ?{0, 0,01, 0,03, 0,06, 0,10},definir? =?escalaz
?
yconstruirb .Deestemodo,? = 0,03representaunruidocuyadesviaciónestándaresaproxi-
?
madamenteel3%deltamañotípicodelasmediciones.
b) Para cada nivel de ruido, utilizar np.linalg.lstsq para obtener la reconstrucción x que mi-
(cid:98)?
?2
nimiza ?A 7 x?b ? . Registrar en una tabla la norma del ruido, la norma del residuo, el error
relativorespectodex ylosvaloresmínimoymáximodelareconstrucción.Visualizarlas
fantoma
imágenesreconstruidasutilizandounamismaescaladecolor.
c) Analizarlosresultados:¿cómocambiaelerrordereconstrucciónalaumentar??¿Porquéelre-
siduo deja de ser nulo cuando se agrega ruido? ¿Por qué pueden aparecer valores negativos,
aunqueloscoeficientesdeatenuacióndelfantomaseannonegativos?
7. SinIA Lareconstruccióncomoproyecciónortogonal
ContinuaremosutilizandoA ylasmedicionescon? = 0,06delejercicioanterior.Lareconstrucción
7
por mínimos cuadrados x (cid:98)? no reproduce exactamente las mediciones ruidosas, sino que genera el
| vectorA |                    | x máscercanoab |     | dentrodeCol(A |                                        | ).  |     |                                  |     |
| ------- | ------------------ | -------------- | --- | ------------- | -------------------------------------- | --- | --- | -------------------------------- | --- |
|         |                    | 7(cid:98)?     |     | ?             |                                        | 7   |     |                                  |     |
|         | Calcularelresiduor |                |     | =b ?A         | x yverificarnuméricamenteque(cid:13)AT |     |     | (cid:13) r ?(cid:13)?0. (cid:13) |     |
| a)      |                    |                | ?   | ?             | 7 (cid:98)?                            |     |     | 7                                |     |
b) Explicarporquéelresultadoanteriorindicaqueelresiduoesortogonalatodaslascolumnasde
|     | A   | yqueA x eslaproyecciónortogonaldeb |     |     |     | sobreCol(A | ).  |     |     |
| --- | --- | ---------------------------------- | --- | --- | --- | ---------- | --- | --- | --- |
|     | 7   | 7(cid:98)?                         |     |     |     | ?          | 7   |     |     |
c) Explicarladiferenciaentrelasproyeccionestomográficasmedidasporelescánerylaproyección
ortogonalqueapareceenmínimoscuadrados.
10

8. Asistenciaparadestrabar/corregir Mínimoscuadradosmediantemétodositerativos
ResolveremosnuevamenteelproblemademínimoscuadradosparaA ylasmedicionesruidosasb ,
|     |     |     | 7   | ?   |
| --- | --- | --- | --- | --- |
peroestavezutilizandométodositerativos.Definimoslafunciónobjetivocomo:
1 ?2.
|     | E(x)= | ?A x?b |     |     |
| --- | ----- | ------ | --- | --- |
2 7 ?
Paraestecosto,utilizaremoselgradiente?E(x)=AT(A x?b )ylaHessianaconstanteH =ATA .
|     |     | 7 7 ? |     | 7 7 |
| --- | --- | ----- | --- | --- |
a) InterpretarlasexpresionesprovistaseimplementarfuncionesquecalculenE(x)y?E(x).Cons-
| truirtambiénH yverificarnuméricamentequeessimétrica. |     |     |     |     |
| ---------------------------------------------------- | --- | --- | --- | --- |
Implementargradientedescendientemediantex = x ???E(x ),comenzandoconx = 0.
| b)  |     | t+1 t | t   | 0   |
| --- | --- | ----- | --- | --- |
Registrarelcostoylanormadelgradientedurantelasiteraciones.
c) Calcularelmayorautovalorµ deH yprobartasasdeaprendizajedentroyfueradelintervalo
ma´x
0<?<2/µ m´ax .Compararlascurvasdelcostoydescribircuándoelmétodoconvergeosevuelve
inestable.
d) Implementar el método de Newton resolviendo Hp = ?E(x ) mediante np.linalg.solve y
t t
actualizandox t+1 =x t ?p t .Verificarquealcanzalasoluciónenunasolaiteraciónyexplicarpor
quéestoocurreparaunafuncióncuadráticaconHessianaconstante.
e) Compararlasreconstruccionesobtenidasmediantegradientedescendiente,Newtonylstsq.
9. IAnecesaria Reconstruccióntomográficaconregularizacióncuadrática
ContinuaremostrabajandoconlamatrizA yelfantomax .Elobjetivoseráestudiarcómocam-
7 fantoma
bialareconstrucciónalmodificartantoelnivelderuidocomolaintensidaddelaregularización.Para
medirlasvariacionesentrepíxelesvecinosconstruiremosunamatrizD.Recordemosque,alvectorizar
| laimagenporfilas,elpíxel(i,j)ocupalaposiciónk |     | =in+j delvectorx. |     |     |
| --------------------------------------------- | --- | ----------------- | --- | --- |
LamatrizDestaráformadapordosbloques:
(cid:18) (cid:19)
D
D = h .
D v
Cada fila de D calculará una diferencia horizontal X ? X . Para ello, contendrá un ?1 en la
h i,j+1 ij
columnakyun1enlacolumnak+1.CadafiladeD calcularáunadiferenciaverticalX ?X
v i+1,j ij ,
por lo que contendrá un ?1 en la columna k y un 1 en la columna k + n. Existen n(n ? 1) pares
horizontalesyn(n?1)paresverticales.Porlotanto,D ? R2n(n?1)×n2 .Paran = 8,susdimensiones
serán 112×64. El vector Dx contendrá primero todas las diferencias horizontales y luego todas las
verticales.
Paracadanivelderuido?ycadavalor??0,definimos
|     |        | 1(cid:16)        | (cid:17) |     |
| --- | ------ | ---------------- | -------- | --- |
|     | J (x)= | ?A x?b ?2+??Dx?2 | ,        |     |
|     | ?,?    | 2 7 ?            |          |     |
cuyogradienteestádadopor
|     | ?J (x)=AT(A | x?b )+?DTDx. |     |     |
| --- | ----------- | ------------ | --- | --- |
|     | ?,?         | 7 7 ?        |     |     |
a) Implementarmatriz_diferencias(n)siguiendolaconstrucciónanterior.Verificarquecadafila
contieneexactamenteun?1yun1,ycomprobarqueparan=8seobtieneD ?R112×64.
b) CrearenPythonelvectorx_constante = np.ones(n**2),querepresentaunaimagenenlaque
todoslospíxelestienenvalor1.VerificarqueDx =0yexplicarporquétodaslasdiferen-
constante
| ciasresultannulas.Compararesteresultadocon?Dx |     | ?.  |     |     |
| --------------------------------------------- | --- | --- | --- | --- |
fantoma
c) Interpretar las expresiones provistas. Explicar qué mide cada término de J , qué representa
?,?
?Dx?ycuáleselpapelde?.
11

d) Reutilizarlasmedicioneslimpiasb =A x yelvectoraleatoriozdelejerciciosobreruido.
7 7 fantoma
Generarmedicionespara? ?{0, 0,10, 0,20}mediante:
escala = np.sqrt(np.mean(b7**2))
b_sigma = b7 + sigma * escala * z
Utilizarelmismovectorzenlostrescasos,demodoquesólocambielaintensidaddelruido.
e) AdaptargradientedescendienteparaminimizarJ paracadacombinaciónde? ?{0, 0,10, 0,20}
?,?
y??{0, 0,1, 0,3, 1}.Comenzarsiempreconx =0.
0
f) Evaluar distintas tasas de aprendizaje y seleccionar una que produzca un descenso estable del
costo.Implementaruncriteriodeparadabasadoenlanormadelgradienteoenlavariacióndel
costo,juntoconunacantidadmáximadeiteraciones.Elcriteriodeparadanodebeutilizarelerror
respectodelfantoma.
g) Registrardurantelasiteracioneselerrorrelativoe =?x ?x ?/?x ?.Paracadanivel
t t fantoma fantoma
deruido,graficare enfuncióndelasiteracionesparalosdistintosvaloresde?.
t
h) Organizarlasreconstruccionesfinalesenunagrilla:cadafiladebecorresponderaunvalorde?y
cadacolumna,aunvalorde?.Utilizarlamismaescaladecolorentodaslasimágenes,sinajustar
automáticamenteloslímitesdecadapanel,eindicarelerrorrelativosobrecadareconstrucción.
i) Resumirloserroresrelativosylasnormasdelosresiduosenunatablaomapadecalorcuyosejes
sean?y?.Paracadanivelderuido,identificarquévalorde?produjoelmenorerror.
j) Analizar los resultados. ¿En qué casos la regularización mejoró la reconstrucción obtenida con
? = 0?¿Cómocambióelvalormásconvenientede?alaumentarelruido?¿Quéefectoprodujo
unaregularizaciónexcesiva?
10. IAnecesaria Aplicaciónfinal:reconstruirunaimagendesconocida
Enlosprimerosejerciciosutilizamosunmodelobinario,enelquecadaentradadeAindicabasola-
mentesielcentrodeunpíxelpertenecíaonoaunrayo.Enesteúltimoejercicioabandonaremosese
modelosimplificadoyutilizaremosunamatrizdeproyecciónbasadaenlageometríadelosrayos.
La entrada a representa la longitud del tramo del rayo r contenido dentro del píxel j. Por lo tan-
rj
to, todo píxel atravesado por el rayo contribuye a la medición de manera proporcional a la longitud
recorridaensuinterior:
(cid:88)
b = a x .
r rj j
j
Losdatosylafuncióndecargaseencuentranenestacarpetadedrive.
Elarchivodatos_tomografia_final_128.npzcontienemedicionessimuladasdeunaimagendesco-
nocidade128×128píxeles.Incluyelaresoluciónn,lamatrizdeproyeccióndispersaA,elvectorde
medicionesruidosasb,losángulos? ,...,? ,lasposicionesdedetectors ,...,s yunaestima-
0 K?1 0 q?1
cióndelruido.Laimagenoriginalpermaneceráocultahastadespuésdelaentrega.
LasfilasdeAylascomponentesdebestánordenadasprimeroporánguloyluegoporposiciónde
detector.Porlotanto,lafilar =kq+jcorrespondealrayo(? ,s ).ComoAesunamatrizdispersa,de-
k j
berácargarsemediantelafunciónprovistaencargar_datos_tomografia.pyynodeberáconvertirse
enunamatrizdensa.
a) CargarlosdatosydeterminarKyqapartirdelascantidadesdeángulosyposicionesdedetector.
VerificarqueA?RKq×n2 yqueb?RKq.
b) OrganizarlasmedicionesenunamatrizS ?RK×q utilizandoS =b .VisualizarScomoun
kj kq+j
sinograma,indicandoensusejeslosvaloresdesy?.
c) Adaptarmatriz_diferencias(n)paraconstruirDcomounamatrizdispersa.VerificarqueD ?
R2n(n?1)×n2
yquecadafilacontieneexactamentedosvaloresnonulos.
d) Obtener una reconstrucción sin regularización, correspondiente a ? = 0, reutilizando el méto-
dodegradientedescendientedesarrolladoanteriormente.Convertirelvectorresultanteenuna
12

imagenden×nyvisualizarla.
e) Obtenerreconstruccionesregularizadasminimizando
1(cid:16) (cid:17)
J (x)= ?Ax?b?2+??Dx?2 .
? 2
Comenzarexplorando? ? {0,1, 1, 10, 30, 100}y,siloconsiderannecesario,probarvaloresadi-
cionales.
f) Paracadareconstrucción,registrar?,latasadeaprendizaje,lacantidaddeiteraciones,lanorma
delresiduo?Ax ?b?,lamedidadevariación?Dx ?ysusvaloresmínimoymáximo.Comparar
(cid:98)? (cid:98)?
lanormadelresiduoconlaestimacióndelruidoincluidaenlosdatos.
g) Elegir la reconstrucción que consideren más adecuada sin conocer la imagen original. Presen-
tarunafiguraconelsinogramaconstruido,lareconstrucciónsinregularizaciónyalmenostres
reconstruccionesregularizadas,utilizandounaescaladecolorcomún.Justificarlaeleccióncon-
siderandoelruido,elresiduo,ylapreservacióndeestructurasenlaimagen.
11. Asistenciaparadestrabar/corregir Reflexiónfinal
Responderbrevemente,apoyándoseenlasecuaciones,losresultadosnuméricosylasvisualizaciones
obtenidasduranteeltrabajo:
a) ¿Porquélareconstruccióntomográficaseconsideraunproblemainverso?Identificarquéinforma-
ciónseconoce,quévariablesebuscarecuperaryquépapelcumpleelmodelob=Ax.
b) ¿Cómopuededeterminarse,apartirdeA,silasmedicionespermitenrecuperarunaúnicaima-
gen?RelacionarlarespuestaconelrangoyelespacionulodeA,yconsiderarquéocurrecuando
existev?=0talqueAv=0.
c) ¿Cómoafectaelruidoalsistemadeecuaciones?Explicarquéproblemaresuelveelmétododemí-
nimoscuadrados,interpretarelresiduoobtenidoydiscutirsisunormaessuficienteparaevaluar
lacalidaddeunareconstrucción.
d)
¿Quésupuestosobrelaimagenintroduceeltérmino?Dx?2
?Analizarcómocambianlasrecons-
truccionesalmodificar?yjustificarlasdiferenciasobservadas.
e) Compararnp.linalg.lstsq,gradientedescendienteyNewtonconsiderandolasiteracionesne-
cesarias, los parámetros que requiere cada método y las soluciones obtenidas. Explicar el com-
portamientoobservadoparaNewton.
f) Elaborar una síntesis que conecte el modelo b = Ax con el rango, el espacio nulo, las proyec-
cionesortogonales,mínimoscuadradosylaregularización.Indicarquéaspectodelproblemade
reconstrucciónpermiteanalizarcadaconcepto.
13
