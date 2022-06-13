# Trénování neuronové sítě a její nasazení s použitím Yolo v5

Detekce objektů je úloha umělé inteligence, která se zaměřuje na detekci objektů v obrazech. Yolo V5 je v současnosti jedním z nejlepších dostupných modelů pro detekci objektů. Skvělé na této hluboké neuronové síti je to, že ji lze velmi snadno přeškolit na vlastní sadě dat.

V tomto dokumentu se budu zabývat tím, jak trénovat model Yolo V5 pro detekci objektů. 

## TOC

<!--ts-->
  * [Základní pojmy](#základní-pojmy)
    * [Yolo v5](#yolo-v5)
    * [COCO Dataset](#coco-dataset)
  * [Tvorba datasetu](#tvorba-datasetu)
    * [Dataset.yaml](#datasetyaml)
    * [Anotovaná data (labels)](#anotovaná-data-labels)
    * [Hierarchie složek](#hierarchie-složek)
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
Project Folder
└───Dataset
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
