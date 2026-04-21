// code.gs
// Đây là logic phía máy chủ của ứng dụng web, xử lý các yêu cầu API.

// Thay thế chuỗi dưới đây bằng Khóa API Gemini của bạn.
const GEMINI_API_KEY = "AIzaSyBpQFaLgBvojVnguDc-Kys5xpPE1DklsZU";

/**
 * Phục vụ tệp HTML khi ứng dụng web được truy cập.
 * @return {HtmlOutput} Nội dung HTML của tệp index.html.
 */
function doGet() {
  return HtmlService.createTemplateFromFile('index').evaluate();
}

/**
 * Gửi yêu cầu đến API Gemini với câu hỏi của người dùng.
 * @param {string} prompt Câu hỏi từ người dùng.
 * @return {string} Phản hồi văn bản từ Gemini.
 */
function processPrompt(prompt) {
  // Cấu hình yêu cầu API
  const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent`;
  const payload = {
    contents: [{
      parts: [{
        text: prompt
      }]
    }]
  };
  
  const options = {
    method: "post",
    contentType: "application/json",
    payload: JSON.stringify(payload),
    muteHttpExceptions: true, // Cho phép xử lý các lỗi HTTP
    headers: {
      'X-goog-api-key': GEMINI_API_KEY // Thêm khóa API vào header
    }
  };
  
  try {
    const response = UrlFetchApp.fetch(url, options);
    const result = JSON.parse(response.getContentText());

    // Xử lý lỗi từ API
    if (result.error) {
      Logger.log("API Error: " + JSON.stringify(result.error));
      const errorMessage = result.error.message || "Không thể nhận phản hồi từ Gemini API.";
      return `Lỗi API: ${errorMessage}`;
    }

    // Trích xuất văn bản từ phản hồi
    if (result.candidates && result.candidates[0] && result.candidates[0].content && result.candidates[0].content.parts) {
      const text = result.candidates[0].content.parts[0].text;
      return text;
    } else {
      Logger.log("Unexpected API response structure: " + JSON.stringify(result));
      return "Không thể trích xuất văn bản từ phản hồi của API. Cấu trúc phản hồi không như mong đợi.";
    }
  } catch (e) {
    Logger.log("General Error: " + e.message);
    return `Đã xảy ra lỗi khi gọi API: ${e.message}`;
  }
}

/**
 * Gửi yêu cầu đến API Gemini để trích xuất thông tin sản phẩm và trả về JSON.
 * @param {string} customerQuery Câu hỏi của khách hàng.
 * @return {string} Chuỗi JSON chứa thông tin sản phẩm.
 */
function processPromptWithJson(customerQuery) {
  // Định nghĩa prompt để trích xuất thông tin sản phẩm
  const prompt = `Dựa vào câu hỏi của khách hàng, hãy phân tích và trích xuất Tên sản phẩm, Dung tích, Màu sắc. Nếu không có thông tin nào, hãy để giá trị là 'null'. Trả về kết quả ở dạng JSON.

  Câu hỏi: ${customerQuery}`;

  // Cấu hình yêu cầu API
  const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent`;
  const payload = {
    contents: [{
      parts: [{
        text: prompt
      }]
    }],
    generationConfig: {
      responseMimeType: "application/json"
    }
  };

  const options = {
    method: "post",
    contentType: "application/json",
    payload: JSON.stringify(payload),
    muteHttpExceptions: true,
    headers: {
      'X-goog-api-key': GEMINI_API_KEY // Thêm khóa API vào header
    }
  };
  
  try {
    const response = UrlFetchApp.fetch(url, options);
    const result = JSON.parse(response.getContentText());

    if (result.error) {
      Logger.log("API Error: " + JSON.stringify(result.error));
      const errorMessage = result.error.message || "Không thể nhận phản hồi từ Gemini API.";
      return JSON.stringify({ error: errorMessage });
    }

    if (result.candidates && result.candidates[0] && result.candidates[0].content && result.candidates[0].content.parts) {
      const jsonResponse = result.candidates[0].content.parts[0].text;
      
      // Thêm dòng này để in phản hồi JSON vào log
      Logger.log("Gemini API JSON response: " + jsonResponse);
      
      return jsonResponse;
    } else {
      Logger.log("Unexpected API response structure: " + JSON.stringify(result));
      return JSON.stringify({ error: "Cấu trúc phản hồi không như mong đợi." });
    }
  } catch (e) {
    Logger.log("General Error: " + e.message);
    return JSON.stringify({ error: `Đã xảy ra lỗi khi gọi API: ${e.message}` });
  }
}
