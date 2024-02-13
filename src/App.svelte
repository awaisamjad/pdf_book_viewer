<script>
  import { log } from "plotly.js-dist";
  import Navbar from "./lib/Navbar.svelte";
</script>

<Navbar />
<main class="container">
  <script
    src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.0.943/pdf.min.js"
  >
  </script>
  <div class="main-title-container">
    <h1 class="main-title">Welcome to PDF BOOK READER!</h1>
    <div class="upload-button">
      <input type="file" id="file" accept="image/*,.pdf" />
      <label for="file">Upload PDF</label>
      <div id="navigation_controls">
        <button id="go_previous">Previous</button>
        <input id="current_page" value="1" type="number" />
        <button id="go_next">Next</button>
      </div>
      <div id="zoom_controls">
        <button id="zoom_in">+</button>
        <button id="zoom_out">-</button>
      </div>
      <div id="canvas_container">
        <canvas id="pdf_renderer"></canvas>
      </div>

      <script>
        var selectedFileName;
        var FileState = {
          file: null,
          currentPage: 1,
          zoom: 1,
        };

        pdfjsLib.getDocument("./hear_doc.pdf").then((file) => {
          FileState.file = file;
          render();
        });
        function render() {
          FileState.file.getPage(FileState.currentPage).then((page) => {
            var canvas = document.getElementById("pdf_renderer");
            var ctx = canvas.getContext("2d");

            var viewport = page.getViewport(FileState.zoom);
            canvas.width = viewport.width;
            canvas.height = viewport.height;

            page.render({
              canvasContext: ctx,
              viewport: viewport,
            });
          });
        }
        document
          .getElementById("go_previous")
          .addEventListener("click", (e) => {
            if (FileState.file == null || FileState.currentPage == 1) return;
            FileState.currentPage -= 1;
            document.getElementById("current_page").value =
              FileState.currentPage;
            render();
          });
        document.getElementById("go_next").addEventListener("click", (e) => {
          if (
            FileState.file == null ||
            FileState.currentPage > FileState.file._pdfInfo.numPages
          )
            return;
          FileState.currentPage += 1;
          document.getElementById("current_page").value = FileState.currentPage;
          render();
        });
        document
          .getElementById("current_page")
          .addEventListener("keypress", (e) => {
            if (FileState.file == null) return;

            // Get key code
            var code = e.keyCode ? e.keyCode : e.which;

            // If key code matches that of the Enter key
            if (code == 13) {
              var desiredPage =
                document.getElementById("current_page").valueAsNumber;

              if (
                desiredPage >= 1 &&
                desiredPage <= FileState.file._pdfInfo.numPages
              ) {
                FileState.currentPage = desiredPage;
                document.getElementById("current_page").value = desiredPage;
                render();
              }
            }
          });
        document.getElementById("zoom_in").addEventListener("click", (e) => {
          if (FileState.file == null) return;
          FileState.zoom += 0.5;
          render();
        });
        document.getElementById("zoom_out").addEventListener("click", (e) => {
          if (FileState.file == null) return;
          FileState.zoom -= 0.5;
          render();
        });
        document.getElementById("file").addEventListener("change", function () {
          selectedFileName = this.files[0].name;
          console.log(selectedFileName);
        });
      </script>
    </div>
  </div>
</main>

<style>
  .logo.vite:hover {
    filter: drop-shadow(0 0 2em #747bff);
  }

  .logo.svelte:hover {
    filter: drop-shadow(0 0 2em #ff3e00);
  }
  main {
    outline: 5px solid #ff3e00;
  }
  #canvas_container {
    width: 800px;
    height: 450px;
    overflow: auto;
  }
</style>
