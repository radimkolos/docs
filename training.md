# Trénování neuronové sítě a její nasazení s použitím Yolo v5

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

COCO je rozsáhlý dataset objektových detekcí, segmentací a popisů objektů.

## Export datasetu

* V Annotation SW si vyfiltrovat požadovaná data.

    * Především zvolit správný Annotation type, to jsou třídy, které chci mít v datasetu, například HUMN.
    * Pozor, cokoli odfiltruji, nemusí být ve výsledném datasetu. Například pokud si vyfiltruji pouze testovací data, nebude v datasetu trénovací množina).




