<!-- index.html -->
<!-- Giao diện người dùng cho ứng dụng web. -->
<!DOCTYPE html>
<html>
  <head>
    <base target="_top">
    <title>Ứng dụng Gemini API</title>
    <!-- Tải Tailwind CSS để tạo giao diện đẹp và đáp ứng -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
      /* Đảm bảo chiều cao tối thiểu của body để căn giữa nội dung */
      body {
        min-height: 100vh;
      }
    </style>
  </head>
  <body class="bg-gray-100 flex items-center justify-center p-4">
    <div class="bg-white p-8 rounded-xl shadow-lg w-full max-w-2xl">
      <h1 class="text-3xl font-bold text-center text-gray-800 mb-6">Trò chuyện với Gemini</h1>

      <!-- Khu vực nhập liệu chung -->
      <div class="flex flex-col space-y-4 mb-6">
        <textarea
          id="promptInput"
          rows="4"
          class="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 transition-shadow"
          placeholder="Nhập câu hỏi của bạn ở đây..."
        ></textarea>
        
        <!-- Nút gửi câu hỏi thông thường -->
        <button
          id="submitButton"
          class="w-full bg-blue-600 text-white font-semibold py-3 rounded-lg hover:bg-blue-700 transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2"
        >
          Gửi câu hỏi
        </button>

        <!-- Nút gửi câu hỏi để trích xuất JSON -->
        <button
          id="submitJsonButton"
          class="w-full bg-green-600 text-white font-semibold py-3 rounded-lg hover:bg-green-700 transition-colors focus:outline-none focus:ring-2 focus:ring-green-500 focus:ring-offset-2 mt-2"
        >
          Trích xuất thông tin sản phẩm (JSON)
        </button>
      </div>

      <!-- Khu vực hiển thị phản hồi văn bản -->
      <div
        id="responseDiv"
        class="bg-gray-50 p-4 rounded-lg border border-gray-200 text-gray-700 whitespace-pre-wrap overflow-y-auto max-h-96 mb-4"
      >
        <!-- Phản hồi văn bản sẽ hiển thị ở đây -->
        <p class="text-gray-400 text-center">Phản hồi văn bản sẽ xuất hiện ở đây.</p>
      </div>
      
      <!-- Khu vực hiển thị phản hồi JSON -->
      <div
        id="jsonResponseDiv"
        class="bg-gray-50 p-4 rounded-lg border border-gray-200 text-gray-700 whitespace-pre-wrap overflow-y-auto max-h-96"
      >
        <!-- Phản hồi JSON sẽ hiển thị ở đây -->
        <p class="text-gray-400 text-center">Phản hồi JSON sẽ xuất hiện ở đây.</p>
      </div>
      
      <!-- Loading spinner -->
      <div id="loadingSpinner" class="flex justify-center mt-4 hidden">
        <div class="w-8 h-8 border-4 border-blue-400 border-t-transparent rounded-full animate-spin"></div>
      </div>
    </div>

    <script>
      // Lắng nghe sự kiện khi tài liệu đã được tải xong
      document.addEventListener('DOMContentLoaded', function() {
        const promptInput = document.getElementById('promptInput');
        const submitButton = document.getElementById('submitButton');
        const submitJsonButton = document.getElementById('submitJsonButton');
        const responseDiv = document.getElementById('responseDiv');
        const jsonResponseDiv = document.getElementById('jsonResponseDiv');
        const loadingSpinner = document.getElementById('loadingSpinner');

        // Thêm trình xử lý sự kiện cho nút "Gửi câu hỏi"
        submitButton.addEventListener('click', function() {
          processPrompt(promptInput.value, 'text');
        });

        // Thêm trình xử lý sự kiện cho nút "Trích xuất JSON"
        submitJsonButton.addEventListener('click', function() {
          processPrompt(promptInput.value, 'json');
        });

        // Thêm trình xử lý sự kiện cho phím Enter trong ô nhập liệu
        promptInput.addEventListener('keydown', function(event) {
          if (event.key === 'Enter' && !event.shiftKey) {
            event.preventDefault(); // Ngăn hành động xuống dòng mặc định
            processPrompt(promptInput.value, 'text'); // Mặc định gửi câu hỏi văn bản
          }
        });

        function processPrompt(prompt, type) {
          const trimmedPrompt = prompt.trim();
          if (trimmedPrompt === '') {
            alert('Vui lòng nhập câu hỏi của bạn.');
            return;
          }

          // Hiển thị loading spinner và xóa nội dung cũ
          responseDiv.innerHTML = '';
          jsonResponseDiv.innerHTML = '';
          loadingSpinner.classList.remove('hidden');
          submitButton.disabled = true; // Vô hiệu hóa nút để tránh gửi nhiều lần
          submitJsonButton.disabled = true;

          if (type === 'json') {
            // Gọi hàm `processPromptWithJson` trong `code.gs`
            google.script.run
              .withSuccessHandler(function(response) {
                loadingSpinner.classList.add('hidden');
                submitButton.disabled = false;
                submitJsonButton.disabled = false;
                try {
                  const parsedJson = JSON.parse(response);
                  jsonResponseDiv.innerHTML = `<pre>${JSON.stringify(parsedJson, null, 2)}</pre>`;
                } catch (e) {
                  jsonResponseDiv.innerHTML = `<p class="text-red-500">Lỗi phân tích JSON: ${response}</p>`;
                }
              })
              .withFailureHandler(onFailure)
              .processPromptWithJson(trimmedPrompt);
          } else {
            // Gọi hàm `processPrompt` trong `code.gs`
            google.script.run
              .withSuccessHandler(function(response) {
                loadingSpinner.classList.add('hidden');
                submitButton.disabled = false;
                submitJsonButton.disabled = false;
                responseDiv.innerHTML = `<p>${response}</p>`;
              })
              .withFailureHandler(onFailure)
              .processPrompt(trimmedPrompt);
          }
        }

        // Xử lý khi API trả về lỗi
        function onFailure(error) {
          loadingSpinner.classList.add('hidden');
          submitButton.disabled = false;
          submitJsonButton.disabled = false;
          responseDiv.innerHTML = `<p class="text-red-500">Đã xảy ra lỗi: ${error.message}</p>`;
          jsonResponseDiv.innerHTML = `<p class="text-red-500">Đã xảy ra lỗi: ${error.message}</p>`;
        }
      });
    </script>
  </body>
</html>
