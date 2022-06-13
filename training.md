# Trénování neuronové sítě a její nasazení s použitím Yolo v5

## TOC

<!--ts-->
   * [Základní pojmy](#ZakladniPojmy)
   * [Export datasetu](#ExportDatasetu)
 !--te-->

## Základní pojmy

[Yolo v5](https://github.com/ultralytics/yolov5) - je množina předtrénvaných modelů na datasetech COCO. YOLO, což je zkratka pro "You only look once", je algoritmus pro detekci objektů, který rozděluje obrázky do mřížkového systému. Každá buňka v mřížce je zodpovědná za detekci objektů uvnitř sebe.

YOLO je jedním z nejznámějších algoritmů detekce objektů díky své rychlosti a přesnosti.

## Export datasetu

* V Annotation SW si vyfiltrovat požadovaná data.

    * Především zvolit správný Annotation type, to jsou třídy, které chci mít v datasetu, například HUMN.
    * Pozor, cokoli odfiltruji, nemusí být ve výsledném datasetu. Například pokud si vyfiltruji pouze testovací data, nebude v datasetu trénovací množina).




