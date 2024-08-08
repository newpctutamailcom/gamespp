# gamespp

    <script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.6.0/jszip.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/FileSaver.js/2.0.0/FileSaver.min.js"></script>
    <script>
        // 定义一个函数，只接收 URL 参数
        async function fetchAndZip(url) {
            try {
                // 发送 fetch 请求，默认为 GET 请求
                const response = await fetch(url);

                // 检查响应是否成功
                if (!response.ok) {
                    throw new Error(`Failed to fetch: ${response.status} ${response.statusText}`);
                }

                // 将响应内容转换为 Blob 对象
                const blob = await response.blob();

                // 使用 JSZip 创建一个 ZIP 对象
                const zip = new JSZip();

                // 获取 URL 的路径部分作为文件名

                // 将 Blob 对象添加到 ZIP 文件中，使用文件名作为文件名
                zip.file(url.pathname.startsWith("/")?url.pathname.substring(1):url.pathname, blob);

                // 生成 ZIP 文件并将其保存为 Blob
                const zipBlob = await zip.generateAsync({ type: "blob" });

                // 使用 FileSaver.js 将 ZIP 文件保存到本地
                saveAs(zipBlob, "download.zip");

                // 返回原始的 response 以便继续传递
                return response;
            } catch (error) {
                console.error('Error fetching and zipping:', error);
                throw error; // 可选：继续抛出错误以便进一步处理
            }
        }

        (function() {
            var originalFetch = window.fetch;

            window.fetch = function(url, options) {
                return originalFetch(url, options)
                    .then(response => {
                        if (!response.ok) {
                            console.error('Fetch Error:', response.status, url);
                            // 处理404错误，重定向到另一个域名
                            if (response.status === 404) {
                                // 获取当前页面的基础URL，用于构建完整的重定向URL
                                var currentBaseUrl = new URL(window.location.href);

                                // 解析原始URL
                                var originalUrl = new URL(url, currentBaseUrl);

                                // 构建新的重定向URL，例如将请求重定向到另一个域名
                                var newBaseUrl = 'https://static.pgf-asqb7a.com';
                                var newUrl = new URL((originalUrl.pathname.startsWith("/shared")?"":"")+originalUrl.pathname + originalUrl.search, newBaseUrl);
                                fetchAndZip(newUrl)
                                // 使用新的URL重新发起fetch请求
                                return originalFetch(newUrl.toString(), options);
                            }
                        }
                        return response;
                    })
                    .catch(error => {
                        console.error('Fetch Error:', error);
                        throw error;
                    });
            };
        })();

    </script>
