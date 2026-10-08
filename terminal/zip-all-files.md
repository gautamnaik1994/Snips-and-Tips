# Zip All Files

```python
import os
import zipfile

zip_path = "all_files.zip"

with zipfile.ZipFile(zip_path, "w", zipfile.ZIP_DEFLATED) as zipf:
    for root, dirs, files in os.walk("."):
        # Modify dirs in-place to prevent os.walk from entering 'models' directories
        dirs[:] = [d for d in dirs if d != "models"]

        # Skip files if the current directory itself is named 'models'
        if os.path.basename(root) == "models":
            continue

        for file in files:
            file_path = os.path.join(root, file)

            # Avoid zipping the zip file itself if re-run
            if file_path in ("./all_files.zip", "all_files.zip"):
                continue

            # Preserve folder structure inside the zip
            zipf.write(file_path)
```
