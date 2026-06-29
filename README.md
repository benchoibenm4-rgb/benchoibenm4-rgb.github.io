<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=yes" />
    <title>Physics MC Past Paper Searcher</title>
    <style>
        /* ── reset & base ── */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            background: #f4f7fc;
            font-family: 'Segoe UI', Roboto, system-ui, -apple-system, sans-serif;
            padding: 0 20px 60px;
            color: #1e293b;
        }

        /* ── fixed search bar ── */
        .search-wrapper {
            position: sticky;
            top: 0;
            z-index: 100;
            background: #ffffff;
            padding: 20px 24px 18px;
            border-radius: 0 0 24px 24px;
            box-shadow: 0 8px 30px rgba(0, 0, 0, 0.07);
            margin: 0 -20px 32px;
            backdrop-filter: blur(6px);
            background: rgba(255, 255, 255, 0.92);
        }
        .search-inner {
            max-width: 900px;
            margin: 0 auto;
            display: flex;
            flex-wrap: wrap;
            align-items: flex-end;
            gap: 14px 18px;
        }
        .search-title {
            width: 100%;
            font-size: 1.6rem;
            font-weight: 700;
            color: #0b2b4a;
            letter-spacing: -0.3px;
            margin-bottom: 2px;
        }
        .search-title span {
            color: #2d7fc1;
        }
        .field-group {
            flex: 1 1 160px;
            min-width: 120px;
        }
        .field-group label {
            display: block;
            font-size: 0.8rem;
            font-weight: 600;
            text-transform: uppercase;
            letter-spacing: 0.4px;
            color: #5b6f87;
            margin-bottom: 4px;
        }
        .field-group input {
            width: 100%;
            padding: 10px 14px;
            border: 2px solid #e2e8f0;
            border-radius: 12px;
            font-size: 1rem;
            background: #fafcff;
            transition: 0.2s;
            outline: none;
            color: #0b2b4a;
        }
        .field-group input:focus {
            border-color: #2d7fc1;
            box-shadow: 0 0 0 4px rgba(45, 127, 193, 0.15);
            background: #fff;
        }
        .field-group input::placeholder {
            color: #a0b3cc;
        }
        .btn-search {
            flex: 0 0 auto;
            padding: 10px 34px;
            background: #1a4b6d;
            border: none;
            border-radius: 12px;
            color: #fff;
            font-weight: 600;
            font-size: 1rem;
            cursor: pointer;
            transition: 0.2s;
            letter-spacing: 0.3px;
            box-shadow: 0 4px 12px rgba(26, 75, 109, 0.25);
            margin-bottom: 0;
            align-self: flex-end;
        }
        .btn-search:hover {
            background: #0f3a56;
            transform: translateY(-1px);
            box-shadow: 0 6px 20px rgba(26, 75, 109, 0.35);
        }
        .btn-search:active {
            transform: translateY(0);
        }

        /* ── result area ── */
        .result-area {
            max-width: 900px;
            margin: 0 auto;
        }

        /* ── cards ── */
        .result-card {
            background: #ffffff;
            border-radius: 24px;
            padding: 28px 28px 32px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.04);
            margin-top: 20px;
            border: 1px solid #eef2f7;
        }
        .result-card .meta {
            display: flex;
            flex-wrap: wrap;
            gap: 8px 24px;
            font-size: 0.9rem;
            color: #3d5775;
            margin-bottom: 18px;
            padding-bottom: 14px;
            border-bottom: 1px solid #edf2f8;
        }
        .result-card .meta strong {
            color: #0b2b4a;
        }
        .result-card .meta .badge {
            background: #e6f0fa;
            padding: 2px 14px;
            border-radius: 30px;
            font-weight: 600;
            font-size: 0.8rem;
            color: #1a4b6d;
        }

        .video-wrapper {
            position: relative;
            padding-bottom: 56.25%; /* 16:9 */
            height: 0;
            border-radius: 16px;
            overflow: hidden;
            background: #0b1a2a;
            box-shadow: 0 4px 16px rgba(0, 0, 0, 0.08);
        }
        .video-wrapper iframe {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            border: 0;
        }

        .photo-container {
            margin-top: 20px;
            border-radius: 16px;
            overflow: hidden;
            background: #f1f5f9;
            display: flex;
            align-items: center;
            justify-content: center;
            min-height: 120px;
            border: 1px solid #e8edf4;
        }
        .photo-container img {
            width: 100%;
            height: auto;
            display: block;
            max-height: 600px;
            object-fit: contain;
            background: #fff;
        }

        /* ── not found ── */
        .not-found {
            background: #fff5f5;
            border: 1px solid #fed7d7;
            border-radius: 20px;
            padding: 32px 28px;
            text-align: center;
            color: #9b2c2c;
            font-size: 1.1rem;
            font-weight: 500;
            margin-top: 20px;
        }
        .not-found::before {
            content: "🔍 ";
            font-size: 1.4rem;
        }

        /* ── empty state ── */
        .empty-state {
            text-align: center;
            padding: 50px 20px 30px;
            color: #6b7f99;
        }
        .empty-state .icon {
            font-size: 3.2rem;
            margin-bottom: 12px;
        }
        .empty-state p {
            font-size: 1rem;
            max-width: 400px;
            margin: 0 auto;
            line-height: 1.6;
        }

        /* ── responsive ── */
        @media (max-width: 640px) {
            .search-wrapper {
                padding: 16px 16px 14px;
                margin: 0 -16px 20px;
                border-radius: 0 0 18px 18px;
            }
            .search-title {
                font-size: 1.2rem;
            }
            .field-group {
                flex: 1 1 100%;
                min-width: 0;
            }
            .btn-search {
                flex: 1 1 100%;
                justify-content: center;
                text-align: center;
                padding: 12px;
            }
            .result-card {
                padding: 18px 16px 22px;
            }
            body {
                padding: 0 12px 40px;
            }
        }
    </style>
</head>
<body>

    <!-- ====== FIXED SEARCH BAR ====== -->
    <div class="search-wrapper">
        <div class="search-inner">
            <div class="search-title">
                📘 Physics MC <span>Past Paper Solution Searcher</span>
            </div>
            <div class="field-group">
                <label for="yearInput">Year</label>
                <input type="text" id="yearInput" placeholder="e.g. 2016" autocomplete="off" />
            </div>
            <div class="field-group">
                <label for="qInput">Question #</label>
                <input type="text" id="qInput" placeholder="e.g. 2 or 02" autocomplete="off" />
            </div>
            <button class="btn-search" id="searchBtn">Search</button>
        </div>
    </div>

    <!-- ====== RESULT AREA ====== -->
    <div class="result-area" id="resultArea">
        <div class="empty-state" id="emptyState">
            <div class="icon">📖</div>
            <p>Enter a year and question number above to find the video solution and photo.</p>
        </div>
        <div id="resultContent"></div>
    </div>

    <script>
        // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
        //  DATA  –  updated with direct i.postimg.cc links
        // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

        const DATA = {
            2016: [
                { q: 1, video: 'https://youtu.be/rY0eMYapszk?si=rUT3vCkpcQ4Vg7xW', photo: 'https://i.postimg.cc/MZc12SWY/2016-DSE-PHY-MC-Q01-sol.png' },
                { q: 2, video: 'https://youtu.be/bU52CD27MZ8?si=0FUll8BVcLD7Eu7W', photo: 'https://i.postimg.cc/SQXcFhy1/2016-DSE-PHY-MC-Q02-sol.png' },
                { q: 3, video: 'https://youtu.be/JQ4WkSZWIB0?si=86MUPVAQaVtYzDU4', photo: 'https://i.postimg.cc/XNZ9SbVs/2016-DSE-PHY-MC-Q03-sol.png' },
                { q: 4, video: 'https://youtu.be/6S2Xl25kQQ4?si=_mF8iQ5utaRJpo9M', photo: 'https://i.postimg.cc/HWJbDgpS/2016-DSE-PHY-MC-Q04-sol.png' },
                { q: 5, video: 'https://youtu.be/JLsOiTwP4g4?si=Mv7it5OTupnpdCSs', photo: 'https://i.postimg.cc/J7yjVL16/2016-DSE-PHY-MC-Q05-sol.png' },
                { q: 6, video: 'https://youtu.be/I2zU1cdXku4?si=unNmfNlygWTJ1kCj', photo: 'https://i.postimg.cc/BZ8290qR/2016-DSE-PHY-MC-Q06-sol.png' },
                { q: 7, video: 'https://youtu.be/iX54c0AsLTI?si=TyIZVt-GbeCyamkG', photo: 'https://i.postimg.cc/2jqnRDzX/2016-DSE-PHY-MC-Q07-sol.png' },
                { q: 8, video: 'https://youtu.be/4emCMQ_kt0k?si=g-6j1QuBsKcwCqlu', photo: 'https://i.postimg.cc/7PCS8kxW/2016-DSE-PHY-MC-Q08-sol.png' },
                { q: 9, video: 'https://youtu.be/lQi2abTpfAQ?si=YEhULLyFWpcaIPUV', photo: 'https://i.postimg.cc/CMZbTgFW/2016-DSE-PHY-MC-Q09-sol.png' },
                { q: 10, video: 'https://youtu.be/YVPLxn2ZN4c?si=fINHoRgK58w95K2l', photo: 'https://i.postimg.cc/J7yjVLrW/2016-DSE-PHY-MC-Q10-sol.png' },
                { q: 11, video: 'https://youtu.be/yX2uXpx5ScQ?si=oIWhlIDg62dVf15e', photo: 'https://i.postimg.cc/L4qjKp9d/2016-DSE-PHY-MC-Q11-sol.png' },
                { q: 12, video: 'https://youtu.be/vp-qghU14eM?si=YpzQc8tY6o5Zjw7N', photo: 'https://i.postimg.cc/c1KfySxW/2016-DSE-PHY-MC-Q12-sol.png' },
                { q: 13, video: 'https://youtu.be/xVaBgiWfgQM?si=bY3mR9Y9AQStgA2F', photo: 'https://i.postimg.cc/KcKtS2GF/2016-DSE-PHY-MC-Q13-sol.png' },
                { q: 14, video: 'https://youtu.be/ZXN5DRgQlEk?si=vDQ3O26BDZEbz1IT', photo: 'https://i.postimg.cc/nVsqbJFn/2016-DSE-PHY-MC-Q14-sol.png' },
                { q: 15, video: 'https://youtu.be/iBqMhnoZ_Cc?si=AZiwT68FWII0cy26', photo: 'https://i.postimg.cc/T2ZgT4wF/2016-DSE-PHY-MC-Q15-sol.png' },
                { q: 16, video: 'https://youtu.be/SGWN3m4vpZ4?si=D0AmjWkf31BGI8Rx', photo: 'https://i.postimg.cc/xjWM94CB/2016-DSE-PHY-MC-Q16-sol.png' },
                { q: 17, video: 'https://youtu.be/XLK1g63opIA?si=az910IMu2YU6qa-S', photo: 'https://i.postimg.cc/RCjKvDFj/2016-DSE-PHY-MC-Q17-sol.png' },
                { q: 18, video: 'https://youtu.be/xgMzY7v0hBw?si=paxBV9VBbYeLVu1x', photo: 'https://i.postimg.cc/RCjKvDh5/2016-DSE-PHY-MC-Q18-sol.png' },
                { q: 19, video: 'https://youtu.be/ttLxWn6FS-s?si=qwhp6BGRFl1xLa0p', photo: 'https://i.postimg.cc/PfBYdRJk/2016-DSE-PHY-MC-Q19-sol.png' },
                { q: 20, video: 'https://youtu.be/wi87UrYqUwM?si=ngOXdOAa5XF_Tzeo', photo: 'https://i.postimg.cc/pVgzP6rH/2016-DSE-PHY-MC-Q20-sol.png' },
                { q: 21, video: 'https://youtu.be/1ooK0tdwU1E?si=B7AtSn03CGO4vxNa', photo: 'https://i.postimg.cc/bYKkqBrP/2016-DSE-PHY-MC-Q21-sol.png' },
                { q: 22, video: 'https://youtu.be/oixrEDudTqk?si=f8_owjWqDYaaJdLK', photo: 'https://i.postimg.cc/NGWmQzFQ/2016-DSE-PHY-MC-Q22-sol.png' },
                { q: 23, video: 'https://youtu.be/pSTwWOMAso8?si=7ABEhEEH-XAGESfH', photo: 'https://i.postimg.cc/3rPm3cN7/2016-DSE-PHY-MC-Q23-sol.png' },
                { q: 24, video: 'https://youtu.be/-oXjIqiympw?si=LMfy8-aS3Zs2BeDv', photo: 'https://i.postimg.cc/qB9yp5qz/2016-DSE-PHY-MC-Q24-sol.png' },
                { q: 25, video: 'https://youtu.be/0H0vA2tARAQ?si=XhAvHBJYCllngQ0W', photo: 'https://i.postimg.cc/1RLwyjX8/2016-DSE-PHY-MC-Q25-sol.png' },
                { q: 26, video: 'https://youtu.be/-s3TQsI983M?si=VgONVrjPNqHRfkSv', photo: 'https://i.postimg.cc/mZ0CT5kr/2016-DSE-PHY-MC-Q26-sol.png' },
                { q: 27, video: 'https://youtu.be/pU7u9AiWM6U?si=grRtI99Lk5nvN3id', photo: 'https://i.postimg.cc/nV6BnPrs/2016-DSE-PHY-MC-Q27-sol.png' },
                { q: 28, video: 'https://youtu.be/sHWkLaM2g8U?si=QmsXm3kaUMJqNHOd', photo: 'https://i.postimg.cc/44rVXSyV/2016-DSE-PHY-MC-Q28-sol.png' },
                { q: 29, video: 'https://youtu.be/0SdkZH8MLSo?si=GLvTLGFEaGNPMOxa', photo: 'https://i.postimg.cc/xTMH83Nc/2016-DSE-PHY-MC-Q29-sol.png' },
                { q: 30, video: 'https://youtu.be/6UyeXa7RbKM?si=KfPG3oPNdLpbiMrb', photo: 'https://i.postimg.cc/MKVRHmfH/2016-DSE-PHY-MC-Q30-sol.png' },
                { q: 31, video: 'https://youtu.be/ELvxks9edh0?si=7-HlYX9EN6a9jKkn', photo: 'https://i.postimg.cc/HsX5n98r/2016-DSE-PHY-MC-Q31-sol.png' },
                { q: 32, video: 'https://youtu.be/nlZS-6lUMCE?si=qeyvT9u5ci-8IH1v', photo: 'https://i.postimg.cc/xTMH83NJ/2016-DSE-PHY-MC-Q32-sol.png' },
                { q: 33, video: 'https://youtu.be/PqndblbduCM?si=PUS6BLNkIZ9PUT-y', photo: 'https://i.postimg.cc/J4ZJ05HH/2016-DSE-PHY-MC-Q33-sol.png' }
            ],
            2017: [
                { q: 1, video: 'https://youtu.be/2kSWREBKtdY?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/cJbMw4xM/2017-DSE-PHY-MC-Q01-sol.png' },
                { q: 2, video: 'https://youtu.be/EQYq--NZc38?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/TP4qgYdN/2017-DSE-PHY-MC-Q02-sol.png' },
                { q: 3, video: 'https://youtu.be/cntc-HJ24s8?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/HLh4XsYS/2017-DSE-PHY-MC-Q03-sol.png' },
                { q: 4, video: 'https://youtu.be/rXQvGeZ9SDk?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/5tRqLN4k/2017-DSE-PHY-MC-Q04-sol.png' },
                { q: 5, video: 'https://youtu.be/Vl9JZW_pu14?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/KYHrBvGH/2017-DSE-PHY-MC-Q05-sol.png' },
                { q: 6, video: 'https://youtu.be/0tabgDP3zbU?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/zBHwjZvw/2017-DSE-PHY-MC-Q06-sol.png' },
                { q: 7, video: 'https://youtu.be/Tnq7pXdc5gs?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/Pxvb4sJz/2017-DSE-PHY-MC-Q07-sol.png' },
                { q: 8, video: 'https://youtu.be/vf_BIFX8BGA?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/W3qmnLzw/2017-DSE-PHY-MC-Q08-sol.png' },
                { q: 9, video: 'https://youtu.be/Ysw54-gSIGc?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/QtKgbGCq/2017-DSE-PHY-MC-Q09-sol.png' },
                { q: 10, video: 'https://youtu.be/uWpVHwSWedY?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/pT5QZbrZ/2017-DSE-PHY-MC-Q10-sol.png' },
                { q: 11, video: 'https://youtu.be/1JDzTEtzyzg?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/zBHwjZvp/2017-DSE-PHY-MC-Q11-sol.png' },
                { q: 12, video: 'https://youtu.be/YHwpORv7JjI?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/hjQ8rWvs/2017-DSE-PHY-MC-Q12-sol.png' },
                { q: 13, video: 'https://youtu.be/P9WiKLPtyiA?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/4dHv5kyB/2017-DSE-PHY-MC-Q13-sol.png' },
                { q: 14, video: 'https://youtu.be/yA5RetIfo_0?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/mD13wfkX/2017-DSE-PHY-MC-Q14-sol.png' },
                { q: 15, video: 'https://youtu.be/3iTJ4y_YT8E?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/26LdwRy9/2017-DSE-PHY-MC-Q15-sol.png' },
                { q: 16, video: 'https://youtu.be/AQrDsVeJ--4?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/qR6cLVqm/2017-DSE-PHY-MC-Q16-sol.png' },
                { q: 17, video: 'https://youtu.be/iX3aNAlC5Ao?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/NM284qFP/2017-DSE-PHY-MC-Q17-sol.png' },
                { q: 18, video: 'https://youtu.be/GsmQOiunRBM?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/d1ZRBP3f/2017-DSE-PHY-MC-Q18-sol.png' },
                { q: 19, video: 'https://youtu.be/Rw1Uk6hXnl8?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/RFJ1R5h8/2017-DSE-PHY-MC-Q19-sol.png' },
                { q: 20, video: 'https://youtu.be/9XqgVthdLL0?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/QtKgbGCw/2017-DSE-PHY-MC-Q20-sol.png' },
                { q: 21, video: 'https://youtu.be/VhrYY9ndYls?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/W3qmnLzx/2017-DSE-PHY-MC-Q21-sol.png' },
                { q: 22, video: 'https://youtu.be/fVru29UL79U?si=RYcFLW7kKmg8QOUt', photo: 'https://i.postimg.cc/4dHv5kng/2017-DSE-PHY-MC-Q22-sol.png' },
                { q: 23, video: 'https://youtu.be/0GMV5-OGDGY?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/sXGPcdxk/2017-DSE-PHY-MC-Q23-sol.png' },
                { q: 24, video: 'https://youtu.be/FIRZQgB11b4?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/26LdwR3p/2017-DSE-PHY-MC-Q24-sol.png' },
                { q: 25, video: 'https://youtu.be/Kx7ysbQ55oE?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/NM284qLt/2017-DSE-PHY-MC-Q25-sol.png' },
                { q: 26, video: 'https://youtu.be/nsi0YIRpikE?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/k4RNc3D9/2017-DSE-PHY-MC-Q26-sol.png' },
                { q: 27, video: 'https://youtu.be/aSD-Avgd9uE?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/sXGPcd1f/2017-DSE-PHY-MC-Q27-sol.png' },
                { q: 28, video: 'https://youtu.be/jvtZtBAkFXg?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/85fdHQsP/2017-DSE-PHY-MC-Q28-sol.png' },
                { q: 29, video: 'https://youtu.be/HIHt_kt6Q6c?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/VvyqZGby/2017-DSE-PHY-MC-Q29-sol.png' },
                { q: 30, video: 'https://youtu.be/jDd-Zd-MaXI?si=Somob-1wzFW0I4GB', photo: 'https://i.postimg.cc/QCLQY4K2/2017-DSE-PHY-MC-Q30-sol.png' },
                { q: 31, video: 'https://youtu.be/A_HNFNt7efQ?si=1HJQR6Jkw8jSet1I', photo: 'https://i.postimg.cc/903dg8qH/2017-DSE-PHY-MC-Q31-sol.png' },
                { q: 32, video: 'https://youtu.be/bwV5A4Gw4eo?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/0jgYXWJv/2017-DSE-PHY-MC-Q32-sol.png' },
                { q: 33, video: 'https://youtu.be/I1uF0vY-DVY?list=PL-s3hs9FhdsrwD8mE5tQwPWzclSX9rkoV', photo: 'https://i.postimg.cc/Gt0Fg7s1/2017-DSE-PHY-MC-Q33-sol.png' }
            ]
        };

        // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
        //  HELPERS
        // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

        /** Extract YouTube video ID from a youtu.be or youtube.com URL */
        function extractVideoId(url) {
            // 匹配常见的 YouTube 链接格式，捕获 11 位 ID
            const patterns = [
                /youtu\.be\/([a-zA-Z0-9_-]{11})(?:\?|&|$)/,
                /youtube\.com\/embed\/([a-zA-Z0-9_-]{11})/,
                /youtube\.com\/v\/([a-zA-Z0-9_-]{11})/,
                /youtube\.com\/watch\?v=([a-zA-Z0-9_-]{11})/
            ];
            for (const pattern of patterns) {
                const match = url.match(pattern);
                if (match) return match[1];
            }
            return null;
        }

        /** Normalize question input: "2" or "02" → 2 */
        function normalizeQuestion(input) {
            const num = parseInt(input, 10);
            return isNaN(num) ? null : num;
        }

        /** Build YouTube embed iframe with best mobile compatibility */
        function youtubeEmbedHtml(videoId) {
            // 使用 youtube-nocookie.com，去除所有追踪参数，添加 playsinline 等
            const embedSrc = `https://www.youtube-nocookie.com/embed/${videoId}?rel=0&modestbranding=1&playsinline=1`;
            return `<iframe
                        src="${embedSrc}"
                        allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
                        allowfullscreen
                        loading="lazy"
                        title="Physics MC solution video"
                    ></iframe>`;
        }

        // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
        //  RENDER
        // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

        const resultContent = document.getElementById('resultContent');
        const emptyState = document.getElementById('emptyState');
        const yearInput = document.getElementById('yearInput');
        const qInput = document.getElementById('qInput');
        const searchBtn = document.getElementById('searchBtn');

        function renderResult(year, qNum, entry) {
            emptyState.style.display = 'none';

            const videoId = extractVideoId(entry.video);
            const directImg = entry.photo; // already a direct i.postimg.cc link

            let videoHtml = '';
            if (videoId) {
                videoHtml = `
                            <div class="video-wrapper">
                                ${youtubeEmbedHtml(videoId)}
                            </div>
                        `;
            } else {
                videoHtml = `
                            <div style="background:#f1f5f9;border-radius:16px;padding:30px;text-align:center;color:#6b7f99;">
                                ⚠️ Video link could not be embedded.
                            </div>
                        `;
            }

            const html = `
                        <div class="result-card">
                            <div class="meta">
                                <span><strong>Year</strong> ${year}</span>
                                <span><strong>Question</strong> ${qNum}</span>
                                <span class="badge">📹 + 🖼️</span>
                            </div>
                            ${videoHtml}
                            <div class="photo-container">
                                <img
                                    src="${directImg}"
                                    alt="Solution photo for ${year} Q${qNum}"
                                    loading="lazy"
                                    onerror="this.style.display='none'; this.parentElement.innerHTML='<div style=\\'padding:30px;text-align:center;color:#9b2c2c;\\'>⚠️ Image could not be loaded.<br><small style=\\'color:#6b7f99;\\'>${entry.photo}</small></div>';"
                                />
                            </div>
                        </div>
                    `;

            resultContent.innerHTML = html;
        }

        function renderNotFound(year, qNum) {
            emptyState.style.display = 'none';
            resultContent.innerHTML = `
                        <div class="not-found">
                            Past Paper solution not found, please check your input again.
                            <div style="margin-top:8px;font-size:0.9rem;font-weight:400;color:#7a4a4a;">
                                Year: <strong>${year}</strong> &nbsp;·&nbsp; Question: <strong>${qNum}</strong>
                            </div>
                        </div>
                    `;
        }

        function renderEmpty() {
            emptyState.style.display = 'block';
            resultContent.innerHTML = '';
        }

        // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
        //  SEARCH LOGIC
        // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

        function performSearch() {
            const yearRaw = yearInput.value.trim();
            const qRaw = qInput.value.trim();

            if (!yearRaw && !qRaw) {
                renderEmpty();
                return;
            }

            const yearNum = parseInt(yearRaw, 10);
            if (isNaN(yearNum) || (yearNum !== 2016 && yearNum !== 2017)) {
                renderNotFound(yearRaw || '???', qRaw || '???');
                return;
            }

            const qNum = normalizeQuestion(qRaw);
            if (qNum === null) {
                renderNotFound(yearNum, qRaw || '???');
                return;
            }

            const yearData = DATA[yearNum];
            if (!yearData) {
                renderNotFound(yearNum, qNum);
                return;
            }

            const entry = yearData.find(item => item.q === qNum);
            if (!entry) {
                renderNotFound(yearNum, qNum);
                return;
            }

            renderResult(yearNum, qNum, entry);
        }

        // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
        //  EVENT BINDING
        // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

        searchBtn.addEventListener('click', performSearch);
        yearInput.addEventListener('keydown', (e) => { if (e.key === 'Enter') performSearch(); });
        qInput.addEventListener('keydown', (e) => { if (e.key === 'Enter') performSearch(); });

        // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
        //  INIT
        // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

        renderEmpty();
        console.log('📘 Physics MC Past Paper Searcher loaded (optimized for mobile).');
        console.log(`📊 2016: ${DATA[2016].length} questions, 2017: ${DATA[2017].length} questions.`);
    </script>

</body>
</html>
