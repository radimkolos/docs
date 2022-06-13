h1. Postup trénování a nasazení vlastního modelu

Dokument popisuje postup jak *exportovat dataset* z anotovaných dat Annotation SW, *trénování pomocí Yolo V5* skriptu, *export* do formátu *ONNX* (či jiného) a použití modelu v Obstacle detectoru.

h2. Export datasetu

* V Annotation SW si vyfiltrovat požadovaná data. Především zvolit správné *Annotation type* (třídy, které chci mít v datasetu). Pozor, cokoli odfiltruji, nemůže být ani ve výsledném datasetu (např. pokud si vyfiltruji pouze testovací data, nebude v datasetu trénovací množina).

* V liště ASW: *File -> Settings* nastavit export configuration, především *rozlišení exportovaných snímků*. (Delší strana, kratší se dopočítá automaticky podle aspect ratio).

* *File -> Export to dataset*, zvolit umístění a exportovat (export nějakou dobu zabere, aplikace při něm rádoby zamrzne, postup exportu lze vidět pouze při spuštění v terminálu). Po dokončení se zobrazí zpráva o úspěšnosti operace.

* Na zvoleném umístění najdete složku */dataset* obsahující obrázky a anotace ve formátu Yolo V5.
** Snímky s nastaveným "Is testing -> true" jsou vždy v */test* množině a slouží pro finální ohodnocení modelu.
** Ostatní snímky "Is testing -> false" jsou náhodně rozděleny do */train* a */val* množin v předem daném poměru a slouží pro trénování a úpravu hyperparametrů trénování modelu.
** Soubor *files_mapping.txt* obsahuje mapování vygenrovaným názvů souborů na názvy originální (pro případ potřeby dohledání originálního snímku v databázi).

* Export v této chvíli *nedokáže zohlednit žádné další nastavené vlastnosti objektů* (např. stavy návěstidel apod.), jelikož není zřejmé jak by s němi měl naložit. To je proto ponecháno k rozřešení dalším generacím.

h2. Trénování a export modelu

* Rozchodit si YoloV5 podle návodu https://github.com/ultralytics/yolov5 (ideálně použít python venv)

* Umístit dataset do Yolo struktury a upravit názvy / cesty (z pohledu /yolov5 napr takto, pokud chceme zachovat předdefinovanou strukturu):
** dataset.yaml prejmenovat podle obsahu a umistit do slozky *./yolov5/data/X.yaml*
** data (img a annot) umistit do *./yolov5/../datasets/X/*
** v dataset.yaml upravit cestu (path) k datum na *../datasets/X*

* Trénování modelu pomocí: *@python train.py --img X --batch X --epochs X --data /path/to/dataset/sign.yaml --weights yolov5s.pt@* (Podrobný popis trénování na https://github.com/ultralytics/yolov5/wiki/Train-Custom-Data)
** Kde *img* je rozlišení vstupních obrázků (např 640 nebo 1280)
** *batch* je počet snímků v jedné trénovací sadě (nastvit co nejvíce podle paměti karty např. 8, 16)
** *epochs* je počet iterací trénovacího algoritmu (např. 50)

* Natrénovaný model najdeme v *yolov5/runs/train/expX/weight/best.pt*

* Vyhodnocení modelu na testovací množině můžeme provést následovně *@python val.py --img 1280 --data sign.yaml --weights runs/train/exp11/weights/best.pt --task test@*
** Parametry stejné jako u trénování
** *--task* určuje kterou množinu chceme vyhodnotit {train, val, test}

* *Export* modelu do formátu *onnx* provedeme pomocí *@python export.py --weights runs/train/expX/weights/best.pt --include onnx --img-size 1280@* (Je důležité nezapomenou správně nastavit *img-size*)

* Tento soubor můžeme načíst pomocí *OD*, jen je potřeba správně nastavit *počet detekovaných tříd v configu*, jinak dojde k pádu aplikace!!!

