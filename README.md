# aircraft-mask-segmentation
Amaç, savaş uçaklarının video üzerinden bulunma olasılıklarının en yüksek olduğu pikselleri kırmızı yapan bir heatmap elde etmek. Bunun için Savaş uçakları YOLO dataseti kullanılacaktır. 2 farklı yöntem ile YOLO datasından maskeler eldildi. Bu yeni data ile Simple CNN, ResNet, EfficientNet, MobileNet, Vit ve zaman bağlamı olan sıralı datalarla eğitildiğinde daha iyi sonuç verebilecek 3'lü input adını verdiğim bir model üzerinde eğitim yapıldı. En iyi sonuç U-Net modelinde elde edildi.

Yöntem-1 

Maskelerin oluşturulmasında, her image için o imagein boyutlarında grayscale formatında siyah bir maske oluşturulur. Bu maskede YOLO etiketinde bulunan merkez koordinatındaki piksel beyaz olacak; merkezden uzaklaştıkça, width ve height değerlerinin ortalamasından elde edilen yarıçap değerine ulaşıncaya kadar, her piksel için gri ton değeri 0'dan 255'e kadar artan bir gradyan oluşturuluyor.
<img width="1032" alt="Ekran Resmi 2024-11-12 15 58 52" src="https://github.com/user-attachments/assets/fd6ddd7d-9bda-4ec8-bb7c-3fc75fbe3649">

Yöntem-2

ROI-OTSU benzeri bir thresholding algoritması kullanılarak maskeler elde edildi. Bu yöntemde YOLO datasındaki koordinatlar kullanılarak thresholding yapılacak bölge bir elips içine sınırlandırıldı. Bu sınırın dışında kalan bölgelerin piksel değeri 0 yapıldı. Sınırın içindeki hedef için grayscale formatında eşikleme yapıldı. Tüm data genel olarak 2 aşamadan geçti.

Genel Data Oluştuma

Burda genel olarak tüm maskeler 2 eşik değere göre ayarlanır: mean_circumference* ve mean_intensity*. Mean_circumference elipsin sırındaki pikseller olamsı sebebiyle arka planın piksellerine aittir bu yüzden beyaza yakınsa “Hedef arka plana göre koyudur” yorumu yapılabilir. Bu durumun tam tersi de tam tersi yorumu yapmaya olanak tanır. Hedef arka plana göre koyuysa mean_intensity değerinin altında kalan pikseller beyaz yapılır, hedef arka plana göre açıksa mean_ intensity değerinin üstündeki pikseller beyez yapılır. Yine de istisnai durumlar olabileceğinden (100-120 resimde bir denk gelir) eşik değere göre doğru olması tahmin edilen maskenin tam tersi output_mask_dir2 dizininde oluşturulur. Maskelerin oluşturulması gradyan inişi ile oluşturlan maskelere göre çok daha hızlı hesaplanır ve modelde daha yüksek doğruluğu vardır ama dezavantajı ekstra manuel inceleme gerektirir. 
(*mean_intensity değeri, yarıçapı elipsin yarıçap değerinin yarısı kadar olan bir elipsin ortalama piksel değeridir)
(*mean_circumference değeri, elips içinde kalan en dış piksellerin ortalama değeridir)

<img width="454" alt="image" src="https://github.com/user-attachments/assets/f254f31e-0a1c-4aad-acbb-e9f6edaf5a6c">


Bazı resimler daha ince işlem gerektirir, bu tarz resimler benim datamda 150-200 resimde bir denk geldi. Bu resimler için while döngüsü içerisinde doğru maske seçilir ya da mean_intensity eşik değeri değiştirilir ve tekrar kontrol edilir. 

<img width="454" alt="image" src="https://github.com/user-attachments/assets/181cddf6-d481-4647-80b1-b4d079646822">


Bu maskeler ile eğitilen hafif bir U-Net modeli kullanılarak video segmentasyonu yapıldı. 


<img width="290" alt="Ekran Resmi 2024-11-18 17 15 32" src="https://github.com/user-attachments/assets/7da0a888-0b88-457d-a2a8-e3b87031dabf">




