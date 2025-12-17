# Zip All Files

```python
import zipfile
import os

zip_path = "all_files.zip"

with zipfile.ZipFile(zip_path, 'w', zipfile.ZIP_DEFLATED) as zipf:
    for root, dirs, files in os.walk("."):
        for file in files:
            file_path = os.path.join(root, file)

            # Avoid zipping the zip file itself if re-run
            if file_path == "./all_files.zip":
                continue

            # Preserve folder structure inside the zip
            zipf.write(file_path)
```
