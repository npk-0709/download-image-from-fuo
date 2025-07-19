F12 lên dán vào console và thưởng thức :)


javascript
```
// Thêm thư viện JSZip và FileSaver.js
const script1 = document.createElement('script');
script1.src = 'https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js';
document.head.appendChild(script1);

const script2 = document.createElement('script');
script2.src = 'https://cdnjs.cloudflare.com/ajax/libs/FileSaver.js/2.0.5/FileSaver.min.js';
document.head.appendChild(script2);

async function downloadImagesAsZip() {
    const zip = new JSZip();
    const imagePromises = [];

    const links = Array.from(document.querySelectorAll('a')).filter(a => 
        /\/attachments\/q\d+-webp\./.test(a.href)
    );
    const numberOfImages = links.length;

    const nameZip = (document.title || 'images') + '.zip';

    links.forEach((link, index) => {
        const imageId = `q${index + 1}`;
        const imageUrl = link.href;
        imagePromises.push(
            fetch(imageUrl)
                .then(response => {
                    if (!response.ok) throw new Error(`Lỗi tải ảnh ${imageUrl}`);
                    return response.blob();
                })
                .then(blob => {
                    zip.file(`${imageId}.webp`, blob);
                })
                .catch(error => console.error(`Lỗi khi tải ${imageUrl}:`, error))
        );
    });

    try {
        // Chờ tất cả ảnh tải xong
        await Promise.all(imagePromises);
        
        // Tạo và lưu file zip
        const content = await zip.generateAsync({ type: 'blob' });
        saveAs(content, nameZip);
        console.log(`Đã tải ${numberOfImages} ảnh và lưu thành ${nameZip}`);
    } catch (error) {
        console.error('Lỗi khi tạo hoặc tải file zip:', error);
    }
}

setTimeout(() => {
    downloadImagesAsZip();
}, 1000);
```
