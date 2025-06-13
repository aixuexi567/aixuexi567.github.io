---
title: 404 Not Found
date: 2023-06-13
permalink: /404.html
comments: false
toc: false
---

<style type="text/css">
.not-found {
  text-align: center;
  padding: 100px 0;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
}
.not-found-title {
  font-size: 8em;
  margin: 0;
  color: #6c63ff;
  text-shadow: 2px 2px 0px #e0e0e0;
  letter-spacing: -5px;
}
.not-found-subtitle {
  font-size: 2em;
  color: #333;
  margin: 10px 0 30px;
  font-weight: 300;
}
.not-found-text {
  font-size: 1.2em;
  color: #666;
  margin-bottom: 40px;
  max-width: 600px;
  margin-left: auto;
  margin-right: auto;
  line-height: 1.6;
}
.not-found-button {
  display: inline-block;
  padding: 12px 24px;
  background-color: #6c63ff;
  color: white;
  text-decoration: none;
  border-radius: 30px;
  font-weight: 500;
  transition: all 0.3s ease;
  margin: 0 10px;
  box-shadow: 0 4px 6px rgba(108, 99, 255, 0.2);
}
.not-found-button:hover {
  background-color: #5a52d5;
  transform: translateY(-2px);
  box-shadow: 0 6px 8px rgba(108, 99, 255, 0.3);
}
.not-found-image {
  max-width: 500px;
  margin: 30px auto;
  display: block;
}
.search-container {
  max-width: 500px;
  margin: 30px auto;
}
.search-input {
  width: 70%;
  padding: 10px 15px;
  border: 2px solid #ddd;
  border-radius: 30px 0 0 30px;
  font-size: 16px;
  outline: none;
  transition: border-color 0.3s;
}
.search-input:focus {
  border-color: #6c63ff;
}
.search-button {
  padding: 10px 20px;
  background-color: #6c63ff;
  color: white;
  border: none;
  border-radius: 0 30px 30px 0;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s;
}
.search-button:hover {
  background-color: #5a52d5;
}
@media (max-width: 768px) {
  .not-found-title {
    font-size: 5em;
  }
  .not-found-subtitle {
    font-size: 1.5em;
  }
  .not-found-image {
    max-width: 90%;
  }
}
</style>

<div class="not-found">
  <h1 class="not-found-title">404</h1>
  <h2 class="not-found-subtitle">Oops! Page Not Found</h2>
  
  <img src="https://cdn.jsdelivr.net/gh/aixuexi567/blog-images@main/404-illustration.svg" alt="404 Illustration" class="not-found-image" onerror="this.src='https://i.imgur.com/ocks0JG.png'; this.onerror=null;">
  
  <p class="not-found-text">The page you're looking for seems to have gone on a journey of its own. It might have been moved, deleted, or never existed in the first place.</p>
  
  <div class="search-container">
    <form onsubmit="return searchSite(this);">
      <input type="text" placeholder="Search for content..." class="search-input" id="search-input">
      <button type="submit" class="search-button">Search</button>
    </form>
  </div>
  
  <div>
    <a href="/" class="not-found-button">Go Home</a>
    <a href="/archives" class="not-found-button">Browse Archives</a>
  </div>
</div>

<script>
function searchSite(form) {
  var searchTerm = document.getElementById('search-input').value;
  if (searchTerm) {
    window.location.href = '/search?q=' + encodeURIComponent(searchTerm);
  }
  return false;
}
</script>