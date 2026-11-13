# Swan-Ganz Thermodilution & Extravascular Pressure Transducer (RLC) Simulator

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.XXXXXXX.svg)](https://doi.org/10.5281/zenodo.XXXXXXX)
[![License: CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC_BY--NC--SA_4.0-blue.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/)
[![Zero Dependencies](https://img.shields.io/badge/dependencies-zero-brightgreen.svg)](#-privacy-and-offline-use)
[![Languages](https://img.shields.io/badge/languages-IT%20%7C%20EN%20%7C%20FR%20%7C%20DE%20%7C%20ES-informational.svg)](#-features-for-teachers-and-students)

A free, source-available, browser-based teaching simulator for **pulmonary artery catheterisation (Swan-Ganz)**, **indicator thermodilution (Stewart-Hamilton)**, **mixed venous oximetry (SvO₂ / Fick principle)** and **extravascular pressure transducer dynamics (second-order RLC hydraulic-electrical analogue)**.

Developed by **Fabrizio Maria Spera** as an interdisciplinary laboratory for **biomedical engineering** (thermal and pressure measurements), **medicine** (cardiology and intensive care) and **critical care nursing**.

> **Educational use only.** This is a mathematical teaching simulator, not a medical device. It must never be used for clinical decisions or patient care.

<!-- Add one or two screenshots here, e.g. docs/phase1.png (anatomical plate) and docs/phase2.png (thermodilution curve):
![Phase 1 – catheter positioning](docs/phase1.png)
![Phase 2 – thermodilution](docs/phase2.png)
-->

---

## 🚀 Live web app (GitHub Pages)

* 🖥️ **[Full interdisciplinary simulator (all fields)](https://bizio11.github.io/swan-ganz-simulator/?area=all)**
* ⚙️ **[Biomedical engineering view](https://bizio11.github.io/swan-ganz-simulator/?area=bio)** — Stewart-Hamilton integration, NTC thermistor and Wheatstone bridge, multiplicative error budget, CSV export
* 🩺 **[Medicine view](https://bizio11.github.io/swan-ganz-simulator/?area=med)** — haemodynamic diagnosis, Forrester / Stevenson-Nohria profiles, CPO, PAPi, TPG, PVR in Wood units, therapy choice
* 🏥 **[Critical care nursing view](https://bizio11.github.io/swan-ganz-simulator/?area=inf)** — phlebostatic levelling, hydrostatic offset, atmospheric zeroing, square-wave (fast-flush) test, end-expiratory PAOP reading
* 🔬 **[Extravascular pressure transducer lab (RLC analogue)](https://bizio11.github.io/swan-ganz-simulator/trasduttore-rlc.html)** — RK4 solver, Bode magnitude/phase plots, Gardner f<sub>n</sub>–ζ adequacy chart, slow-motion fluid column and strain-gauge diaphragm

Both apps work on smartphones and tablets.

### 🔗 Links for each group

Add parameters to the address to open the simulator already set up for a group. The **🔗 Share** button in the top bar copies (or, on phones, shares) a link with the current field, advanced mode and phase.

| Parameter | Values | Effect |
|---|---|---|
| `ambito` (or `area`) | `medicina` / `med`, `infermieristica` / `inf`, `bioingegneria` / `bio`, `completo` / `all` (English names also accepted) | Opens that field directly, skipping the selection screen |
| `avanzate` | `1` or `0` | Turns the advanced features (ECG, pacing, SvO₂/Fick) on or off for that visit |
| `lang` | `it`, `en`, `fr`, `de`, `es` | Forces the language for that visit; without it, the browser language (or the user's last choice) is used |
| `#termodiluizione` | — | Opens phase 2 directly (default: `#posizionamento`, phase 1) |

Examples:

* Physicians: `https://bizio11.github.io/swan-ganz-simulator/?ambito=medicina`
* Nurses: `https://bizio11.github.io/swan-ganz-simulator/?ambito=infermieristica`
* Biomedical engineers, advanced features, phase 2: `https://bizio11.github.io/swan-ganz-simulator/?ambito=bioingegneria&avanzate=1#termodiluizione`
* International colleagues: `https://bizio11.github.io/swan-ganz-simulator/?ambito=completo&lang=en`

Parameters set in the link are not saved: they apply to that visit only, and the student can still change field from the top bar.

---

## 🎯 What the simulator computes

All models run in plain JavaScript in the browser, with no external libraries and no network requests.

### 1. Phase 1 · Right-heart catheterisation and pressure waveforms

* **Anatomical plate and X-ray fluoroscopy mode:** interactive advancement (0–55 cm) from the superior vena cava through the right atrium, right ventricle and pulmonary artery to the wedge position (PCWP / PAOP), with beating walls and moving valve leaflets.
* **Pressure waveform synthesis:** periodic functions of the cardiac phase with zero-mean Gaussian a, c, v waves, a sinusoidal respiratory modulation (0.25 Hz) and white noise.
* **Procedural safety tracking:** advancing through the right ventricle with the balloon deflated (ventricular arrhythmias), withdrawing with the balloon inflated (valve injury), balloon inflation beyond 50 cm (pulmonary artery rupture) and wedging longer than 15 s (pulmonary infarction).
* **Advanced electrical module (`⚡ Advanced`, optional):** synthesised lead II ECG; catheter-induced ventricular ectopic beats, sustained ventricular tachycardia and traumatic complete AV block; temporary **Paceport VVI pacing** (rate, output current with a 4 mA capture threshold, 200 bpm overdrive burst); synchronised external cardioversion (150 J).

### 2. Phase 2 · Thermodilution, thermistor physics and error budget

* **Discrete convolution engine:** the plunger push profile is sampled every Δt = 0.05 s and convolved with a unit-area gamma-variate impulse response h(t), normalised with the Lanczos approximation of Γ(α + 1):

  $$\Delta T(t_n) = G \cdot \sum_k q_k \cdot \lambda_k \cdot h(t_n - t_k) + r(t_n) + \varepsilon_n$$

* **NTC thermistor and Wheatstone bridge:** R(T) = R<sub>37</sub> · e<sup>−0.038·(T − 37)</sup>, bridge output in millivolts, and first-order thermal inertia (τ = 0.15 s clean, τ = 1.20 s fibrin-coated) discretised with the implicit (backward) Euler method.
* **Monitor algorithm:** moving-average peak detection, trapezoidal integration to 30 % of the peak, least-squares log-linear fit of the downslope between 80 % and 30 % to obtain τ, and exponential tail extrapolation.
* **Multiplicative error budget:** each measurement is decomposed into five factors relative to the cardiac output set for the patient:

  $$\frac{\text{CO}_{\text{measured}}}{\text{CO}_{\text{set}}} = f_{\text{physiology}} \times f_{\text{settings}} \times f_{\text{indicator}} \times f_{\text{algorithm}} \times f_{\text{noise}}$$

* **Continuous SvO₂ oximetry and Fick principle (`⚡ Advanced`, optional):** fibre-optic reflection spectrophotometry (≈ 660 nm red vs. 805–940 nm near-infrared reference), DO₂, O₂ER, and a side-by-side comparison of thermodilution CO and Fick CO, showing why tricuspid regurgitation distorts the thermal curve but not the oxygen balance.

### 3. Companion module · Extravascular pressure transducer as an RLC circuit (`trasduttore-rlc.html`)

* Solves the second-order equation of the saline-filled catheter and strain-gauge diaphragm with a 4th-order Runge-Kutta scheme, using a "shadow" system without the flush to isolate the free step response of the **fast-flush test**:

  $$L_c C_d \ddot{P}_m + R_c C_d \dot{P}_m + P_m = P_{\text{in}}(t), \qquad f_n = \frac{1}{2\pi\sqrt{L_c C_d}}, \qquad \zeta = \frac{R_c}{2}\sqrt{\frac{C_d}{L_c}}$$

* Two sections: **Swan-Ganz pulmonary artery (110 cm catheter)** and **radial artery (20 G cannula)**, showing *pseudo-wedge* from overdamping (air bubbles, clots) and systolic overshoot from underdamping (long extensions, stopcocks).
* Opened from the Swan-Ganz simulator, it receives language, theme, scenario and pulmonary pressures through the URL and offers a link back.

---

## 🌍 Features for teachers and students

* **Three fields of study, one model:** nursing, medicine and biomedical engineering share the same physical model; each field adds its own tools, exercises and debriefing criteria. A "Complete" view shows everything.
* **Five languages:** Italian, English, French, German and Spanish, switchable at any time.
* **Light and dark theme.**
* **Exam Mode and printable debriefing:** hides the scenario and the true cardiac output, then produces a debriefing report that can be printed or saved as PDF, plus CSV export of the curves. Suitable for formative assessment; it is not tamper-proof, since a student can inspect the page code in the browser.

---

## 🔒 Privacy and offline use

* No cookies, no accounts, no analytics and no server-side processing. Preferences (language, theme, field, advanced mode) are stored only in the browser's `localStorage`.
* Zero external dependencies: no JavaScript or CSS libraries, no web fonts, no requests to third-party servers. Both pages use the device's system fonts and work fully offline.
* To run it locally, download the two `.html` files into the same folder and open `index.html` in a browser.

---

## 📁 Repository contents

| File | Content |
|---|---|
| `index.html` | Main simulator: catheter positioning (phase 1) and thermodilution (phase 2) |
| `trasduttore-rlc.html` | Extravascular pressure transducer lab (RLC analogue) |
| `README.md` | This file |
| `LICENSE.md` | License notice (CC BY-NC-SA 4.0) |
| `CITATION.cff` | Citation metadata read by GitHub ("Cite this repository") |
| `.zenodo.json` | Metadata read by Zenodo when a GitHub release is archived |

---

## 📝 How to cite

If you use the simulator in teaching or research, please cite it. After the first Zenodo release, use the DOI shown in the badge above:

> Spera FM. *Swan-Ganz Thermodilution and Extravascular Pressure Transducer (RLC) Interactive Simulator* [software]. Version 1.0.0. Zenodo; 2026. doi:10.5281/zenodo.XXXXXXX

GitHub also generates the citation automatically from `CITATION.cff` (button **Cite this repository**).

---

## 📖 Key references

1. Swan HJC, Ganz W, Forrester J, Marcus H, Diamond G, Chonette D. Catheterization of the heart in man with use of a flow-directed balloon-tipped catheter. *N Engl J Med*. 1970;283(9):447–451.
2. Ganz W, Donoso R, Marcus HS, Forrester JS, Swan HJC. A new technique for measurement of cardiac output by thermodilution in man. *Am J Cardiol*. 1971;27(4):392–396.
3. Forrester JS, Diamond G, Chatterjee K, Swan HJC. Medical therapy of acute myocardial infarction by application of hemodynamic subsets. *N Engl J Med*. 1976;295(24):1356–1362 and 295(25):1404–1413.
4. Nohria A, Tsang SW, Fang JC, Lewis EF, Jarcho JA, Mudge GH, Stevenson LW. Clinical assessment identifies hemodynamic profiles that predict outcomes in patients admitted with heart failure. *J Am Coll Cardiol*. 2003;41(10):1797–1804.
5. Thompson HK, Starmer CF, Whalen RE, McIntosh HD. Indicator transit time considered as a gamma variate. *Circ Res*. 1964;14:502–515.
6. Gardner RM. Direct blood pressure measurement—dynamic response requirements. *Anesthesiology*. 1981;54(3):227–236.
7. Kleinman B, Powell S, Kumar P, Gardner RM. The fast flush test measures the dynamic response of the entire blood pressure monitoring system. *Anesthesiology*. 1992;77(6):1215–1220.
8. Fick A. Über die Messung des Blutquantums in den Herzventrikeln. *Sitzungsber Phys-Med Ges Würzburg*. 1870;16.
9. Baele PL, McMichan JC, Marsh HM, Sill JC, Southorn PA. Continuous monitoring of mixed venous oxygen saturation in critically ill patients. *Anesth Analg*. 1982;61(6):513–517.
10. Geddes LA. *Handbook of Blood Pressure Measurement*. Clifton (NJ): Humana Press; 1991.

---

## 🇮🇹 In breve

Simulatore didattico interdisciplinare (medicina, infermieristica, ingegneria biomedica) del cateterismo di Swan-Ganz, della termodiluizione, dell'ossimetria SvO₂ / metodo di Fick e della dinamica del trasduttore di pressione. Funziona nel browser, anche da smartphone, in cinque lingue. Strumento esclusivamente didattico: non è un dispositivo medico.

---

## ⚠️ Disclaimer and license

**Educational use only.** This software is a teaching simulator, not a medical device, and must never be used for clinical decision-making or patient care. Waveforms, constants and scenarios are plausible teaching values, not manufacturer data; see *Model limits* in the in-app methodological notes.

© 2026 **Fabrizio Maria Spera**. Released under the **Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International (CC BY-NC-SA 4.0)** license: you may share and adapt it for non-commercial purposes, giving appropriate credit and distributing adaptations under the same license. See [`LICENSE.md`](LICENSE.md).
