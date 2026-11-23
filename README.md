[scraper.py](https://github.com/user-attachments/files/35255298/scraper.py)
# ecommerce-catalog-web-straper
import time
import pandas as pd
import requests
from bs4 import BeautifulSoup

# Mapping teks rating menjadi angka agar mudah dianalisis klien
RATING_MAP = {"one": 1, "Two": 2, "Three": 3, "Four": 4, "Five": 5}
base_url = "https://books.toscrape.com/catalogue/page-{}.html"
products = []
start_time = time.time()
print("Memulai scrapping 1.000 produck...")
# Loop halaman 1 sampai 50(masing-masing 20 produk = 1.000)
for page in range(1,51):
    url = base_url.format(page)
    try:
        response = requests.get(url, timeout=15)
    except requests.RequestException as e:
        print(f"Error koneksi halaman {page}: {e}")
        continue
    response.encoding = "utf-8" 
    if response.status_code != 200:
        print(f"Gagal mengakses halaman {page} (status: {response.status_code})")
        # Memastikan simbol mata uang £ terbaca rapih
        continue

    soup = BeautifulSoup(response.text, "html.parser")
    items = soup.find_all("article", class_="product_pod")

    for item in items:
        # 1. Judul Produk
        title = item.h3.a["title"]

        # 2. Harga (bersihkan simbol £ agar bisa dihitung sebagai angka)
        price_raw = item.find("p", class_="price_color").text.strip()
        price_gbp = float(price_raw.replace("£", ""))

        # 3. Rating Bintang (diambil dari nama class kedua pada tag <p class="star-rating ... ">)
        rating_text = item.find("p", class_="star-rating")["class"][1]
        rating = RATING_MAP.get(rating_text, 0)

        # 4. Status Stok
        stock = item.find("p", class_="instock availability").text.strip()

        # 5. Link Detail Produk
        Link = "https://books.toscrape.com/catalogue/" + item.h3.a["href"]

        products.append({
            "Product Name": title,
            "Price (GBP)": price_gbp,
            "Rating (1-5)": rating,
            "Stock Status": stock,
            "Products URL": Link,
        })
    if page % 10 == 0:
        print(f"Selesai memproses {page} halaman ({len(products)} produk) ...")

# Simpan ke DataFrame Pandas
df = pd.DataFrame(products)

#Ekspor ke excel dan CSV
df.to_excel("books_catalog_1000.xlsx", index=False)
df.to_csv("books_catalog_1000.csv", index=False)

elapsed = round(time.time() - start_time, 2)
print(f"\nSelesai! {len(df)} produk berhasil die[books_catalog_1000.xlsx](https://github.com/user-attachments/files/35255302/books_catalog_1000.xlsx)
kstrak dalam {elapsed} detik.")

<img width="1915" height="1004" alt="preview-catalog" src="https://github.com/user-attachments/assets/93290298-8740-42e9-9350-70d6bfded514" />

