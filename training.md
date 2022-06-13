# Trénování neuronové sítě a její nasazení s použitím Yolo v5

Detekce objektů je úloha umělé inteligence, která se zaměřuje na detekci objektů v obrazech. Yolo V5 je v současnosti jedním z nejlepších dostupných modelů pro detekci objektů. Skvělé na této hluboké neuronové síti je to, že ji lze velmi snadno přeškolit na vlastní sadě dat.

V tomto dokumentu se budu zabývat tím, jak natrénovat model Yolo V5 pro detekci objektů. Jde o mělký počeštěný výcuc z oficiální [dokumentace](https://docs.ultralytics.com/) Yolo v5, doplněný o naše postupy, nicméně by měl obsahovat vše podstatné.

## TOC

<!--ts-->
  * [Základní pojmy](#základní-pojmy)
    * [Yolo v5](#yolo-v5)
    * [COCO Dataset](#coco-dataset)
  * [Tvorba datasetu](#tvorba-datasetu)
    * [Dataset.yaml](#datasetyaml)
    * [Anotovaná data (labels)](#anotovaná-data-labels)
    * [Hierarchie složek](#hierarchie-složek)
  * [Trénování](#trénování)
    * [Výběr modelu](#výběr-modelu)
    * [Příprava Yolo v5](#příprava-yolo-v5)
    * [Export datasetu z anotačního SW](#export-datasetu-z-anotačního-sw)
    * [Úprava cest datasetu](#úprava-cest-datasetu)
    * [Trénování modelu](#trénování-modelu)
<!--te-->

## Základní pojmy

### [Yolo v5](https://github.com/ultralytics/yolov5)

Je množina předtrénvaných modelů na [datasetech coco](#coco-dataset).

YOLO, což je zkratka pro "You only look once", je algoritmus pro detekci objektů, který rozděluje obrázky do mřížkového systému. Každá buňka v mřížce je zodpovědná za detekci objektů uvnitř sebe.

YOLO je jedním z nejznámějších algoritmů detekce objektů díky své rychlosti a přesnosti.

Detailnější popis a dokumentace [zde](https://docs.ultralytics.com/).

### [COCO Dataset](https://cocodataset.org/#home)

COCO (Common Objects in Context) je jednou z nejoblíbenějších sad obrazových dat s aplikacemi, jako je detekce objektů, segmentace a tvorba popisků.

Common Objects in Context (COCO) doslova znamená, že snímky v datovém souboru jsou každodenní objekty zachycené z každodenních scén. To dodává objektům zachyceným ve scénách určitý "kontext".

COCO poskytuje označování více objektů, anotace segmentačních masek, popisky obrázků, detekci klíčových bodů a panoptické segmentační anotace s celkem 81 kategoriemi, což z něj činí velmi univerzální a víceúčelovou datovou sadu.

Nás ovšem nebude až tak zajímat obsah této volně stažitelné [sady dat](https://cocodataset.org/#download), protože si vytvoříme svoji vlastní v kapitole [Tvorba datasetu](#tvorba-datasetu). Převezmeme tudíž pouze hierarchii složek této datové sady.

## Tvorba datasetu

Modely YOLOv5 musí být natrénované na anotovaných datech, aby se naučily třídy objektů v těchto datech.

V našem případě budeme pracovat s naším [azd-annotationsw](http://192.168.54.118/annotationsw/annotationsw) a pomocí tohoto nástroje dataset vytvoříme.

V následující sub kapitolách se dozvíte, jak vypadá hierarchie složek, co a proč vlastně obsahují.

### Dataset.yaml

Tento soubor, jehož obsah je níže, je konfigurační soubor datasetu, který definuje:

  1. Cestu ke kořenovému adresáři datasetu a relativní cesty `path` k adresářům s obrázky `train` / `val` / `test` (nebo soubory *.txt s cestami k obrázkům)

  2. Počet tříd `nc`
  
  3. Seznam názvů tříd `names`

```yaml
# Dataset in YoloV5 format created by AZD Annotation software ©

path: ../datasets/dataset_1 # dataset root dir
train: images/train         # train images
val: images/val             # validation images
test: images/test           # test images

nc: 2
names: [ 'HUMN', 'SIGN' ] # classes names

```

### Anotovaná data (labels)

Ke každému z obrázků v sadě dat patří jeden soubor `*.txt` obsahující anotace.

Specifikace jsou následující:

* Jeden řádek znamená jeden anotovaný objekt
* Každý řádek je definován jako TŘÍDA X_STŘED Y_STŘED VÝŠKA ŠÍŘKA
* Souřadnice boxu musí být v normalizovaném formátu XYWH (od 0 - 1)
* Čísla tříd jsou indexována od nuly

Například takto:

```
0 0.1734375 0.6342593 0.033072915 0.1162037
0 0.19947916 0.6435185 0.018229166 0.125
0 0.2140625 0.6449074 0.0140625 0.10601852
0 0.24895833 0.6467593 0.02734375 0.10185185
0 0.32135418 0.6018519 0.021354167 0.09537037
```

### Hierarchie složek

YOLOv5 předpokládá, že adresář `/dataset_1` je uvnitř adresáře `/datasets` vedle adresáře (naklonovaný repozitář) `/yolov5`. YOLOv5 automaticky vyhledá anotace (labels) pro každý obrázek nahrazením posledního výskytu `/images/` v cestě k jednotlivým obrázkům za `/labels/`. Například:

```
../datasets/coco128/images/im0.jpg  # image
../datasets/coco128/labels/im0.txt  # label
```

Obrázky `*.jpg` a anotace `*.txt` by měly být uspořádány do složek naśledujím způsobem.

```
Datasets
└───Dataset_#
    └───images
    │   └───test
    │   │    │ 000000000009.jpg
    │   │    │ 000000000025.jpg
    │   │    │   ...
    │   └───train   
    │   │    │ 000000000139.jpg
    │   │    │ 000000000285.jpg
    │   │    │   ...
    │   └───val   
    │        │ 00000000003.jpg
    │        │ 00000002128.jpg
    │        │   ...
    └───labels
    │   └───test
    │   │    │ 000000000009.txt
    │   │    │ 000000000025.txt
    │   │    │   ...
    │   └───train   
    │   │    │ 000000000139.txt
    │   │    │ 000000000285.txt
    │   │    │   ...
    │   └───val   
    │        │ 00000000003.txt
    │        │ 00000002128.txt
    │        │   ...
```

## Trénování

V této kapitole by mělo být popsané vše dostatečné k úspěšnému dotrénování kterékoliv z Yolo v5 sití. Více informací pod [tímto](https://github.com/ultralytics/yolov5/wiki/Train-Custom-Data) odkazem.

### Výběr modelu

Vyberte předtrénovaný model, na jehož základě začnete trénovat. Je lepší vybrat síť, která je již předtrénovaná na něčem jiném, než trénovat svoji vlastní síť úplně od začátku. Yolo v5 nabízí tyto předtrénované sítě, jejichž srovnání se dá dohledat [zde](https://github.com/ultralytics/yolov5#pretrained-checkpoints).

![porování sítí](model_comparison.png "Porování Sítí")


### Příprava Yolo v5

Naklonujte si repozitář Yolo v5 a nainstalujte python závislosti. V oficiální dokumentaci je napsané, že je potřeba Python>=3.7.0, nicméně může se stát, že poslední verze nebude kompatibilní s nejnovější verzí PyTorch. Doporučuju tedy stáhnout verzi 3.7 a nebát se experimentovat s verzemi, pokud se nebude cokoliv dařit.

```
yay -S python37
git clone https://github.com/ultralytics/yolov5
cd yolov5
pip install -r requirements.txt
```

### Export datasetu z anotačního SW

V Annotation SW si vyfiltrovat požadovaná data zvolením správného *Annotation type*. Pozor, cokoli odfiltruji, nemusí být ve výsledném datasetu, například pokud si vyfiltruji pouze testovací data, nebude v datasetu trénovací množina.

V liště ASW: *File->Settings* nastavit export configuration, především *rozlišení exportovaných snímků*. Delší strana, kratší se dopočítá automaticky podle aspect ratio.

*File->Export to dataset*, zvolit umístění a exportovat. Export zabere nějaký čas a aplikace při něm rádoby zamrzne. Postup exportu lze vidět pouze při spuštění v terminálu nebo nahlédnutím do složek datasetu. Po dokončení se zobrazí zpráva o úspěšnosti operace.

Na zvoleném umístění najdete složku `/dataset` obsahující obrázky a anotace ve formátu YOLO.

  * Snímky s nastaveným "Is testing->true" jsou vždy v `/test` množině a slouží pro finální ohodnocení modelu.
  * Ostatní snímky "Is Testing->false" jsou náhodně rozděleny do `/train` a `/val` množin v předem daném poměru (15 %) a slouží pro trénování a úpravu hyperparametrů trénování modelu.
  * Soubor *files_mapping.txt* obsahuje mapování vygenrovaným názvů souborů na názvy originální (pro případ potřeby dohledání originálního snímku v databázi).

Export v této chvíli *nedokáže zohlednit žádné další nastavené vlastnosti objektů* (např. stavy návěstidel apod.), jelikož není zřejmé jak by s němi měl naložit. To je proto ponecháno k rozřešení dalším generacím.

### Úprava cest datasetu

1. `dataset_#.yaml` přejmenovat podle potřeb a umístit do složky `/yolov5/data/dataset_#.yaml`
2. Zbytek umístit do složky `/yolov5/../Datasets/dataset_#`
3. V `dataset.yaml` upravit cestu `path` k datům na `../Datasets/dataset_#`

### Trénování modelu

Jak jsem již psal buď se dá dotrénovat kterýkoliv z modelů Yolov5 nebo náhodně inicializovat svůj vlastní model, což ale není doporučeno. 

Předtrénované váhy se automaticky stahují z nejnovější verze YOLOv5.

Příkaz k natrénování sítě:

```bash
$ python train.py --img `#` --batch `#` --epochs `#` --data `#` --weights `#`
```

* `--img` je rozlišení vstupních obrázků (např 640 nebo 1280)
* `--batch` je počet snímků v jedné trénovací sadě (nastvit co nejvíce podle paměti karty např. 8, 16)
* `--epochs` je počet iterací trénovacího algoritmu (např. 100)

Výsledný natrénovaný model/y dohledáte v naklnovaném repozitáři v adresáři `yolov5/runs/train/expX/weight/`. 

Adresář bude obsahovat dvě natrénované sítě a to tu nejlepší natrénovanou `best.pt` a poslední `last.pt` z poslední epochy.
