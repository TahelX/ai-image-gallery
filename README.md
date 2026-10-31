# AI Image Gallery (GitHub Pages)

A static image gallery and JSON manifest designed to let a browser-capable AI agent navigate a collection of video frames.

## Files

- `index.html` — gallery UI, search, selection, full-size viewer, manifest export and agent prompt.
- `images.json` — list of image files and metadata.
- `images/` — put your image files here.
- `CNAME` — custom domain set to `frames.heykadir.in` (change it if you choose a different subdomain).

## Add up to 1,000 images

1. Put image files inside `images/`. Use simple names such as `frame-0001.jpg`, `frame-0002.jpg`, etc.
2. Edit `images.json` so each image has an entry. Example:

   ```json
   {
     "images": [
       {
         "id": "frame-0001",
         "name": "frame-0001.jpg",
         "src": "images/frame-0001.jpg",
         "description": "Frame at 00:00:01",
         "order": 1
       },
       {
         "id": "frame-0002",
         "name": "frame-0002.jpg",
         "src": "images/frame-0002.jpg",
         "description": "Frame at 00:00:02",
         "order": 2
       }
     ]
   }
   ```

3. Commit and push the files to your GitHub repository. The `images.json` file must be at the repository root, next to `index.html`.
4. Open the gallery URL. It reads `images.json` and displays the images.
5. Use **Copy agent prompt** and give the copied prompt to your browser-capable AI agent.

### Important GitHub limits

- GitHub Pages is static hosting; this page does **not** upload files into GitHub.
- GitHub warns against using Pages as free hosting for large commercial or high-bandwidth sites. 1,000 images may work if total size and traffic are modest, but large images can make deployments and browsing slow.
- Git repositories are not a good place for very large media collections. Compress/resize frames first; consider Git LFS or object storage if the files are large. Git LFS has separate usage/hosting constraints and should not be assumed to work for Pages assets in every setup.
- GitHub Pages published sites have a recommended source repository size under 1 GB and a soft bandwidth limit of 100 GB/month. Check GitHub's current documentation before relying on these limits.
- This does not bypass AI model usage limits. The agent must still open and visually inspect images, and it may not be able to process all 1,000 at once.

## Deploy to GitHub Pages

1. Create a public repository, for example `ai-image-gallery`.
2. Upload `index.html`, `images.json`, `CNAME`, and the `images/` directory.
3. In GitHub, open **Settings → Pages**.
4. Under **Build and deployment**, choose **Deploy from a branch**, select `main` and `/ (root)`, then Save.
5. In your DNS provider for `heykadir.in`, create a **CNAME record**:
   - Host/Name: `frames`
   - Target/Value: `tahelx.github.io`
   - TTL: default/automatic
6. In repository **Settings → Pages → Custom domain**, enter `frames.heykadir.in`. Save and wait for DNS verification; enable **Enforce HTTPS** when it becomes available.

Replace `tahelx.github.io` if your GitHub Pages username is different. Do not create a CNAME record at the root/apex domain for this subdomain setup.

## Bulk-add image entries

For 1,000 images, don't hand-write the JSON. This Python script scans `images/` and creates `images.json` in filename order. Run it locally in the repository folder:

```python
from pathlib import Path
import json

folder = Path("images")
extensions = {".jpg", ".jpeg", ".png", ".webp", ".gif"}
files = sorted(p for p in folder.iterdir() if p.is_file() and p.suffix.lower() in extensions)

data = {
    "title": "AI Image Gallery manifest",
    "instructions": "Images are ordered by filename. Use the direct image URLs.",
    "images": [
        {
            "id": p.stem,
            "name": p.name,
            "src": f"images/{p.name}",
            "description": "",
            "order": i
        }
        for i, p in enumerate(files, start=1)
    ]
}
Path("images.json").write_text(json.dumps(data, indent=2), encoding="utf-8")
print(f"Wrote {len(files)} images to images.json")
```

Then commit and push the new manifest and images.

## Suggested agent prompt

> Open this image gallery: `https://frames.heykadir.in/`. Read `https://frames.heykadir.in/images.json`, then inspect the direct image URLs in manageable batches. Keep the filenames and order, summarize the visual sequence, and do not claim to have analyzed images you could not open. If your tools cannot access the manifest or image URLs, explain the limitation.
