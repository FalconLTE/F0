# إنشاء مسار مجلد GitHub Actions داخل مشروع React
workflow_path = Path("/mnt/data/react-product-app/.github/workflows")
workflow_path.mkdir(parents=True, exist_ok=True)

# محتوى ملف workflow لـ CI/CD
ci_cd_content = """
name: React CI/CD

on:
  push:
    branches:
      - main
      - release

jobs:
  build:
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 18

      - name: Install dependencies
        run: npm install

      - name: Build the app
        run: npm run build

      - name: Publish artifact
        uses: actions/upload-artifact@v3
        with:
          name: build
          path: build/

  deploy:
    if: github.ref == 'refs/heads/release'
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 18

      - name: Install dependencies
        run: npm install

      - name: Build
        run: npm run build

      - name: Docker Build and Push
        run: |
          echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin
          docker build -t ${{ secrets.DOCKER_USERNAME }}/react-product-app:v1.0.0 .
          docker push ${{ secrets.DOCKER_USERNAME }}/react-product-app:v1.0.0

  code-scan:
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main' || github.ref == 'refs/heads/release'
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Run ESLint
        run: |
          npm install eslint
          npx eslint . || true
"""

# حفظ الملف
workflow_file = workflow_path / "ci-cd.yml"
workflow_file.write_text(ci_cd_content.strip())

# إعادة ضغط المشروع كاملاً مع ملفات CI/CD
zip_path = "/mnt/data/react-product-app.zip"
with zipfile.ZipFile(zip_path, "w", zipfile.ZIP_DEFLATED) as zipf:
    for path in Path("/mnt/data/react-product-app").rglob("*"):
        zipf.write(path, path.relative_to("/mnt/data"))

zip_path

