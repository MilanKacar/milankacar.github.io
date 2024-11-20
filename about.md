---
layout: page
title: 👨‍💻 About Me
permalink: /about/
---
I am a Vienna-based **Software Engineer** and **Survey Engineer** with over 5 years of professional experience. I hold a degree in **Geodesy** from *Graz University of Technology*, where I contributed as a project and teaching assistant. Currently, I work in the e-commerce and fintech sectors as a **Data Scientist, Engineer, and Analyst** at *Niceshops GmbH* and *Gateway Ventures GmbH*.

Originally from Mrkonjić Grad, Republic of Srpska (BA), I completed high school focusing on advanced studies in **Mathematics, Logic, and Physics**.

---

## 🚀 About this blog:

In this blog, we'll explore a variety of topics. The chart below highlights my personal interests, showing which subjects I’m most passionate about 📊💡:
<!-- Add a canvas element to display the radial chart -->
<canvas id="radialChart" width="400" height="400"></canvas>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script>
  document.addEventListener("DOMContentLoaded", function () {
    const categoryData = {
      {% for category in site.categories %}
        "{{ category | first }}": {{ category[1].size }},
      {% endfor %}
    };

    const ctx = document.getElementById('radialChart').getContext('2d');
    const categories = Object.keys(categoryData);
    const postCounts = Object.values(categoryData);

    // Define chart options based on dark mode
    const getChartOptions = (isDarkMode) => ({
      responsive: true,
      plugins: {
        legend: {
          labels: {
            color: isDarkMode ? '#fff' : '#333', // Legend text color
          }
        }
      },
      scales: {
        r: {
          grid: {
            color: isDarkMode ? 'rgba(255, 255, 255, 0.2)' : 'rgba(128, 128, 128, 0.5)',
          },
          angleLines: {
            color: isDarkMode ? 'rgba(255, 255, 255, 0.3)' : 'rgba(128, 128, 128, 0.7)',
          },
          ticks: {
            color: isDarkMode ? '#fff' : '#000',
            backdropColor: 'rgba(255, 255, 255, 0)',
          }
        }
      }
    });

    let radialChart = null;

    // Function to create or update the chart
    const createChart = (isDarkMode) => {
      if (radialChart) radialChart.destroy();
      radialChart = new Chart(ctx, {
        type: 'polarArea',
        data: {
          labels: categories,
          datasets: [{
            label: 'Number of Posts',
            data: postCounts,
            backgroundColor: [
              'rgba(255, 99, 132, 0.2)',
              'rgba(54, 162, 235, 0.2)',
              'rgba(255, 206, 86, 0.2)',
              'rgba(75, 192, 192, 0.2)',
              'rgba(153, 102, 255, 0.2)',
              'rgba(255, 159, 64, 0.2)',
            ],
            borderColor: [
              'rgba(255, 99, 132, 1)',
              'rgba(54, 162, 235, 1)',
              'rgba(255, 206, 86, 1)',
              'rgba(75, 192, 192, 1)',
              'rgba(153, 102, 255, 1)',
              'rgba(255, 159, 64, 1)',
            ],
            borderWidth: 2,
          }]
        },
        options: getChartOptions(isDarkMode),
      });
    };

    // Function to initialize dark mode on page load
    const initializeDarkMode = () => {
      const savedMode = localStorage.getItem("darkMode");
      if (savedMode === "enabled") {
        document.body.classList.add("dark-mode");
      }
      createChart(document.body.classList.contains("dark-mode"));
    };

    // Function to toggle dark mode
    const toggleDarkMode = () => {
      const isDarkMode = document.body.classList.toggle("dark-mode");
      localStorage.setItem("darkMode", isDarkMode ? "enabled" : "disabled");
      createChart(isDarkMode); // Update chart for dark mode
    };

    // Initialize dark mode on page load
    initializeDarkMode();

    // Add event listener for dark mode toggle button
    const toggleButton = document.getElementById("toggle-mode");
    if (toggleButton) {
      toggleButton.addEventListener("click", toggleDarkMode);
    }
  });
</script>



At the outset, I expect most topics will revolve around software development. However, as this journey unfolds, I’m excited to see how my interests evolve and where this blog takes us. 🚀

*📈 Number of visits according to Google Analytics: <span id="activeUsers"></span>*

--- 

## 🚀 Technical Skills

<table style="border-collapse: collapse; width: 100%; border-radius: 8px; overflow: hidden; box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15);">
  <thead style="background-color: #c4c2bb; color: black;">
    <tr>
      <th style="border: 1px solid #ddd; padding: 12px; text-align: left;">Skill</th>
      <th style="border: 1px solid #ddd; padding: 12px; text-align: left;">Proficiency</th>
      <th style="border: 1px solid #ddd; padding: 12px; text-align: left;">Skill</th>
      <th style="border: 1px solid #ddd; padding: 12px; text-align: left;">Proficiency</th>
    </tr>
  </thead>
  <tbody>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;">Python</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
      <td style="border: 1px solid #ddd; padding: 12px;">PostgreSQL</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 12px;">Google Cloud</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
      <td style="border: 1px solid #ddd; padding: 12px;">MySQL</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;">C/C++</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
      <td style="border: 1px solid #ddd; padding: 12px;">JavaScript</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 12px;">GIS</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
      <td style="border: 1px solid #ddd; padding: 12px;">PHP</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;">Cloud Computing</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
      <td style="border: 1px solid #ddd; padding: 12px;">Linux</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 12px;">GNSS & Signal Processing</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
      <td style="border: 1px solid #ddd; padding: 12px;">Docker</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;">Java</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢</td>
      <td style="border: 1px solid #ddd; padding: 12px;">Apache Spark</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 12px;">Data Structures & Algos</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
      <td style="border: 1px solid #ddd; padding: 12px;">Scala</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;">R</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
      <td style="border: 1px solid #ddd; padding: 12px;">Navigation</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
    </tr>
  </tbody>
</table>

---

## 💼 Work Experience

### **Data Scientist & Engineer**
*Niceshops GmbH*  
📍 Vienna, Austria  
🗓️ Jan 2021 – Present  
- Financial analysis and cloud computing
- AI & ML tasks: demand forecasting, dynamic pricing
- Course instruction and research development
  
*overlaping with Data Engineer / Team Lead position*

### **Data Engineer / Team Lead**  
*Gateway Ventures GmbH*  
📍 Vienna, Austria  
🗓️ Aug 2023 – Present  
- Startup valuation models and pitch deck analysis  
- Data mining, lake construction, and management  
- Leading data scraping projects
  
*overlaping with Data Scientist & Engineer position*

### **Teaching Assistant**  
*TU Graz*  
📍 Graz, Austria  
🗓️ Mar 2021 – Jan 2022  
- Integrated navigation
- Sensor fusion
- Indoor navigation
  
*overlaping with Software Engineering position*

### **Software Engineer**  
*TU Graz*  
📍 Graz, Austria  
🗓️ Sep 2019 – Jan 2022  
- Software architecture and data analysis  
- Project management and ML forecasting  
- Focus on electric vehicle testing and automation
  
*overlaping with Teaching Assistant position*

---

## 📚 Education

### **Master's in Computer Science**
*Graz University of Technology*  
📍 Graz, Austria  
🗓️ 2022 – Present  

### **Master's in Geodesy**
*Graz University of Technology*  
📍 Graz, Austria  
🗓️ 2019 – Present  

### **BSc in Geomatics Engineering**
*Graz University of Technology*  
📍 Graz, Austria  
🗓️ 2015 – 2019  

---

## 🎯 Key Projects

# Projects

- **📈 Stock Prices Predictor (AI)**  
  This project integrates Artificial Intelligence (AI) and Machine Learning (ML) techniques to predict stock price fluctuations by analyzing indices like S&P 500, Dow Jones, and DAX. It focuses on enhancing precision and decision-making in stock price predictions.

- **🎲 Lotto Numbers Predictor (Python)**  
  Developed a computational model using statistical and analytical methods to predict numbers likely to appear in future lottery draws. The project aims to discover patterns in historical data to improve lottery number forecasting.

- **🔍 Scientific Paper Miner - SPaRC (C++)**  
  A platform similar to a search engine, specifically designed for retrieving scientific papers. It serves as a valuable resource for researchers and professionals by offering efficient access to scholarly content.

- **🌳 Pollen Tree Detector (AI/Image Detection)**  
  Automated method for detecting and identifying trees from satellite images, which can help predict seasonal pollen spread in cities. This is especially useful in mitigating seasonal allergies caused by airborne pollen.

- **📡 Different Signal Processing Projects (TSA/Python)**  
  Focused on GNSS signal processing, including encoding and decoding techniques. These projects aim to safeguard against spoofing attacks and analyze GNSS time series data.

- **🌍 Gravity Anomaly Maps (Python/Physics)**  
  A project based on the GRACE mission that uses satellite data to create detailed gravity anomaly maps of Earth, providing insights into mass distribution for climate and environmental studies.

- **📏 Kalman Filter · Advanced Kalman Filter · Particle Filter (Python/Statistics)**  
  Implemented state estimation and sensor fusion techniques for indoor navigation using Kalman Filters and Particle Filters, enhancing accuracy in localization and tracking.

- **💸 Dynamic Pricing Strategy and Competitor Prices Crawler (Python/Proxy/WebCrawling)**  
  Developed dynamic pricing strategies and web crawlers to analyze competitor pricing data, optimizing product prices to boost sales and maintain market competitiveness.

- **📊 Demand Prediction (AI)**  
  Used demand forecasting techniques to predict the demand for products over the coming year, considering factors like seasonality and public holidays. The project aims to improve inventory management and operational decisions.

- **💼 Tinder for Investors (AI) - TL**  
  Developed a platform connecting investors with startups, enabling them to provide assistance and receive equity. Startups can present challenges and receive actionable guidance from investors.

- **🌐 Data Lake Creation (GCP) - TL**  
  Amalgamated various data sources such as Pipedrive, Mixpanel, Google Analytics, and backend datasets to create a comprehensive data lake, providing valuable insights for business decision-making.

- **📝 Pitch Deck Analyzer (AI) - TL**  
  Automated analysis of startup pitch decks, utilizing AI to extract and assess relevant information for investment decisions, aligning the deck with predefined search criteria.

- **💡 Startup Value Estimator (ML/AI) - TL**  
  Developed a machine learning model to estimate startup value based on data submissions. The evaluation process determines whether the startup meets investment criteria.



---

## 🌍 Languages

<table style="border-collapse: collapse; width: 100%; border-radius: 8px; overflow: hidden; box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15);">
  <thead style="background-color: #c4c2bb; color: black;">
    <tr>
      <th style="border: 1px solid #ddd; padding: 12px; text-align: left;">Language</th>
      <th style="border: 1px solid #ddd; padding: 12px; text-align: left;">Proficiency</th>
    </tr>
  </thead>
  <tbody>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;"><strong>Serbian, Croatian, Bosnian</strong></td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 12px;"><strong>English</strong></td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;"><strong>German</strong></td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 12px;"><strong>Russian</strong></td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢</td>
    </tr>
  </tbody>
</table>

---

## 📧 Contact
- **Email**: [milankacar@live.com](mailto:milankacar@live.com)
- **LinkedIn**: [linkedin.com/in/milan-kacar](https://linkedin.com/in/milan-kacar)
- **Website**: [milankacar.github.io/CV](https://milankacar.github.io/CV)

---

## ⚙️ Hobbies
- **📚 Reading**: Aiming for a book a week  
- **🏋️‍♂️ Fitness**: Passionate about fitness and healthy living  
- **✈️ Traveling**: An avid traveler always looking for new adventures  
- **📈 Trading**: Enthusiastic about market analysis and trading strategies
