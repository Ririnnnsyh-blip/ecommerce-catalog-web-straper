[scraper.py](https://github.com/user-attachments/files/35255298/scraper.py)
# ecommerce-catalog-web-straper
Python web scraper built with requests, BeautifulSoup, and pandas to extract book catalog data (Product Name, Price (GBP), Rating (1-5), Stock Status, and Products URL) across paginated catalog pages, clean price and star-rating values, and export structured datasets to Excel (.xlsx) and CSV (.csv).

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
(https://github.com/user-attachments/files/35255302/books_catalog_1000.xlsx)

<img width="1915" height="1004" alt="preview-catalog" src="https://github.com/user-attachments/assets/93290298-8740-42e9-9350-70d6bfded514" />

