# Trénování neuronové sítě a její nasazení s použitím Yolo v5

Detekce objektů je úloha umělé inteligence, která se zaměřuje na detekci objektů v obrazech. Yolo V5 je v současnosti jedním z nejlepších dostupných modelů pro detekci objektů. Skvělé na této hluboké neuronové síti je to, že ji lze velmi snadno přeškolit na vlastní sadě dat.

V tomto dokumentu se budu zabývat tím, jak trénovat model Yolo V5 pro detekci objektů. 

## TOC

<!--ts-->
  * [Základní pojmy](#základní-pojmy)
    * [Yolo v5](#yolo-v5)
    * [COCO Dataset](#coco-dataset)
  * [Export datasetu](#export-datasetu)
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

Nás ovšem nebudou až tak zajímat tato volně stažitelná [sada dat](https://cocodataset.org/#download). To hlavní čemu je potřeba věnovat pozornost je především hierarchie složek této datové sady popsané níže.

  Project Folder
  └───the code notebook (.py / .ipynb)
  │
  └───COCOdataset2017   
      └───images
      │   └───train
      │   │    │   000000000009.jpg
      │   │    │   000000000025.jpg
      │   │    │   ...
      │   └───val   
      │        │   000000000139.jpg
      │        │   000000000285.jpg
      │        │   ...
      └───annotations
          │   instances_train.json
          │   instances_val.json

## Export datasetu

* V Annotation SW si vyfiltrovat požadovaná data.

    * Především zvolit správný Annotation type, to jsou třídy, které chci mít v datasetu, například HUMN.
    * Pozor, cokoli odfiltruji, nemusí být ve výsledném datasetu. Například pokud si vyfiltruji pouze testovací data, nebude v datasetu trénovací množina).




