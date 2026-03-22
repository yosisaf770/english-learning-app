<!DOCTYPE html>
<html lang="he" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vocab Master 500</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- React and Babel -->
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Assistant:wght@300;400;600;700;800&display=swap" rel="stylesheet">
    
    <style>
        body { font-family: 'Assistant', sans-serif; background-color: #fbfcfe; margin: 0; }
        .perspective-1000 { perspective: 1000px; }
        @keyframes pulse-custom { 0%, 100% { opacity: 1; } 50% { opacity: .5; } }
        .animate-pulse-custom { animation: pulse-custom 2s cubic-bezier(0.4, 0, 0.6, 1) infinite; }
    </style>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect, useMemo } = React;

        // רשימת מילים חלקית (לדוגמה בקובץ זה - מומלץ להעתיק את כל ה-500 מהגרסה הקודמת)
        const FINAL_WORD_LIST = [
            { word: "Analyze", translation: "לנתח", phonetic: "אֶ-נָ-לַאייז" },
            { word: "Approach", translation: "גישה / לגשת", phonetic: "אֶ-פְּרוֹ-טְשׁ" },
            { word: "Appropriate", translation: "מתאים, הולם", phonetic: "אֶ-פְּרוֹ-פְּרִי-אֵייט" },
            { word: "Available", translation: "זמין", phonetic: "אֶ-ווֵי-לָ-בְּל" },
            { word: "Benefit", translation: "תועלת", phonetic: "בֶּ-נֶ-פִיט" },
            { word: "Category", translation: "קטגוריה", phonetic: "קָ-טֶ-גוֹ-רִי" },
            { word: "Community", translation: "קהילה", phonetic: "קוֹ-מְיוּ-נִי-טִי" },
            { word: "Complex", translation: "מורכב", phonetic: "קוֹם-פְּלֶקְס" },
            { word: "Concept", translation: "מושג", phonetic: "קוֹן-סֶפְּט" },
            { word: "Conclusion", translation: "מסקנה", phonetic: "קוֹן-קְלוּ-זֶ'ן" },
            { word: "Abstract", translation: "מופשט", phonetic: "אֶבּ-סְטְרֶקְט" },
            { word: "Acknowledge", translation: "להכיר ב-", phonetic: "אֶק-נוֹ-לֶג'" },
            { word: "Aggregate", translation: "מקבץ", phonetic: "אֶ-גְרֶ-גֵייט" },
            { word: "Allocation", translation: "הקצאה", phonetic: "אֶ-לוֹ-קֵי-שֶׁן" },
            { word: "Assignment", translation: "משימה", phonetic: "אֶ-סַאיין-מֶנְט" },
            { word: "Challenge", translation: "אתגר", phonetic: "טְשֶׁ-לֶנְג'" },
            { word: "Cooperate", translation: "לשתף פעולה", phonetic: "קוֹ-אוֹ-פֶּ-רֵייט" },
            { word: "Decade", translation: "עשור", phonetic: "דֶ-קֵייד" },
            { word: "Reveal", translation: "לחשוף", phonetic: "רִי-ווִיל" },
            { word: "Unique", translation: "ייחודי", phonetic: "יוּ-נִיק" },
            { word: "Abandon", translation: "לנטוש", phonetic: "אֶ-בֶּנ-דוֹן" },
            { word: "Abroad", translation: "בחו''ל", phonetic: "אֶ-בְּרוֹד" },
            { word: "Achieve", translation: "להשיג", phonetic: "אֶ-טְשִׁיב" },
            { word: "Advice", translation: "עצה", phonetic: "אֶד-וַאייס" },
            { word: "Budget", translation: "תקציב", phonetic: "בַּא-גֶ'ט" },
            { word: "Campaign", translation: "קמפיין", phonetic: "קֶמ-פֵּיין" },
            { word: "Decision", translation: "החלטה", phonetic: "דִי-סִי-זֶ'ן" },
            { word: "Explain", translation: "להסביר", phonetic: "אִק-סְפְּלֵיין" },
            { word: "Focus", translation: "מיקוד", phonetic: "פוֹ-קוּס" },
            { word: "Goal", translation: "מטרה", phonetic: "גוֹל" }
            // כאן ניתן להוסיף את כל שאר המילים מהרשימות הקודמות
        ];

        function App() {
            const [currentIndex, setCurrentIndex] = useState(0);
            const [showTranslation, setShowTranslation] = useState(false);
            const [learnedWords, setLearnedWords] = useState([]);
            const [sessionWords, setSessionWords] = useState([]);
            const [isFinished, setIsFinished] = useState(false);
            const [searchTerm, setSearchTerm] = useState("");

            useEffect(() => {
                const saved = JSON.parse(localStorage.getItem('english_pro_v500_progress') || '[]');
                setLearnedWords(saved);
                const remaining = FINAL_WORD_LIST.filter(w => !saved.includes(w.word));
                setSessionWords(remaining);
                if (remaining.length === 0) setIsFinished(true);
                lucide.createIcons();
            }, []);

            useEffect(() => { lucide.createIcons(); }, [showTranslation, isFinished, sessionWords]);

            const filteredWords = useMemo(() => {
                return sessionWords.filter(w => w.word.toLowerCase().includes(searchTerm.toLowerCase()));
            }, [sessionWords, searchTerm]);

            const currentWordData = filteredWords[currentIndex];

            const handleNext = () => {
                setShowTranslation(false);
                if (currentIndex < filteredWords.length - 1) {
                    setCurrentIndex(currentIndex + 1);
                } else {
                    setCurrentIndex(0);
                }
            };

            const handleMarkAsLearned = () => {
                if (!currentWordData) return;
                const newLearned = [...learnedWords, currentWordData.word];
                setLearnedWords(newLearned);
                localStorage.setItem('english_pro_v500_progress', JSON.stringify(newLearned));
                const updated = sessionWords.filter(w => w.word !== currentWordData.word);
                setSessionWords(updated);
                setShowTranslation(false);
                if (updated.length === 0) setIsFinished(true);
                else if (currentIndex >= updated.length) setCurrentIndex(0);
            };

            if (isFinished) return (
                <div className="min-h-screen flex items-center justify-center p-6 bg-indigo-50">
                    <div className="bg-white p-12 rounded-[3rem] shadow-2xl text-center max-w-md w-full border border-slate-100">
                        <div className="w-20 h-20 bg-green-100 text-green-600 rounded-full flex items-center justify-center mx-auto mb-6">
                            <i data-lucide="check" style={{width: 48, height: 48}}></i>
                        </div>
                        <h1 className="text-3xl font-black text-slate-800 mb-4">אלופים!</h1>
                        <p className="text-slate-500 mb-8">סיימתם ללמוד את כל המילים ברשימה.</p>
                        <button onClick={() => { localStorage.removeItem('english_pro_v500_progress'); window.location.reload(); }} className="w-full py-5 bg-indigo-600 text-white rounded-3xl font-bold shadow-lg">התחל מחדש</button>
                    </div>
                </div>
            );

            return (
                <div className="min-h-screen py-8 px-4 flex flex-col items-center">
                    <div className="max-w-xl w-full flex justify-between items-center mb-6">
                        <div className="flex items-center gap-2 text-indigo-600">
                            <i data-lucide="book-open" style={{width: 28, height: 28}}></i>
                            <span className="font-black text-xl">Vocab Master</span>
                        </div>
                        <div className="bg-white px-4 py-2 rounded-full shadow-sm text-sm font-bold border border-slate-50">
                            {learnedWords.length} / {FINAL_WORD_LIST.length}
                        </div>
                    </div>

                    <div className="max-w-xl w-full h-2.5 bg-slate-200 rounded-full mb-8 overflow-hidden">
                        <div className="h-full bg-indigo-500 transition-all duration-1000" style={{ width: `${(learnedWords.length / FINAL_WORD_LIST.length) * 100}%` }}></div>
                    </div>

                    <div className="max-w-xl w-full relative mb-8">
                        <input 
                            type="text" 
                            placeholder="חפש מילה ברשימה..." 
                            className="w-full py-5 px-6 bg-white rounded-[2rem] shadow-sm border-none outline-none text-lg focus:ring-2 focus:ring-indigo-100 transition-all"
                            value={searchTerm}
                            onChange={(e) => { setSearchTerm(e.target.value); setCurrentIndex(0); }}
                        />
                    </div>

                    <div className="max-w-xl w-full">
                        {!currentWordData ? (
                            <div className="bg-white p-16 rounded-[3rem] text-center shadow-lg text-slate-400 font-bold">לא נמצאו מילים תואמות</div>
                        ) : (
                            <div 
                                className={`bg-white rounded-[3rem] shadow-2xl p-8 min-h-[450px] flex flex-col items-center justify-center cursor-pointer transition-all duration-500 relative ${showTranslation ? 'ring-4 ring-indigo-50' : ''}`}
                                onClick={() => setShowTranslation(!showTranslation)}
                            >
                                <span className="absolute top-8 right-8 text-[10px] font-black text-slate-300 tracking-[0.2em] uppercase">Intermediate</span>
                                
                                <h2 className={`font-black text-slate-800 transition-all duration-500 text-center ${showTranslation ? 'text-5xl mb-8' : 'text-8xl'}`}>
                                    {currentWordData.word}
                                </h2>

                                {showTranslation && (
                                    <div className="text-center animate-in fade-in slide-in-from-bottom-4 duration-500">
                                        <div className="h-1 w-12 bg-indigo-100 rounded-full mx-auto mb-6"></div>
                                        <p className="text-5xl font-black text-indigo-600 mb-4">{currentWordData.translation}</p>
                                        <div className="inline-flex items-center gap-2 bg-slate-50 px-5 py-2.5 rounded-2xl border border-slate-100">
                                            <i data-lucide="info" style={{width: 18, height: 18}} className="text-indigo-300"></i>
                                            <span className="text-xl font-bold text-slate-500 tracking-wide">{currentWordData.phonetic}</span>
                                        </div>
                                    </div>
                                )}

                                {!showTranslation && (
                                    <div className="mt-16 flex flex-col items-center gap-3 text-indigo-400 animate-pulse-custom">
                                        <i data-lucide="eye" style={{width: 42, height: 42}} strokeWidth={2.5}></i>
                                        <span className="text-xs font-black tracking-widest uppercase">לחץ לתרגום</span>
                                    </div>
                                )}
                            </div>
                        )}
                    </div>

                    <div className="max-w-xl w-full mt-8 grid grid-cols-2 gap-4">
                        <button onClick={handleMarkAsLearned} disabled={!currentWordData} className="py-6 bg-white text-emerald-600 rounded-[2.5rem] border-2 border-emerald-50 font-black text-xl shadow-lg active:scale-95 transition-transform disabled:opacity-50 flex items-center justify-center gap-2">
                            <i data-lucide="check-circle" style={{width: 24, height: 24}}></i>
                            יודע
                        </button>
                        <button onClick={handleNext} disabled={!currentWordData} className="py-6 bg-indigo-600 text-white rounded-[2.5rem] font-black text-xl shadow-xl active:scale-95 transition-transform disabled:opacity-50 flex items-center justify-center gap-2">
                            מילה הבאה
                            <i data-lucide="chevron-right" style={{width: 24, height: 24}}></i>
                        </button>
                    </div>
                </div>
            );
        }

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>
