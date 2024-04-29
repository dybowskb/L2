### Wprowadzenie
Celem zadania było stworzenie algorytmu w środowisku MATLAB, który pozwala obliczyć przybliżone pole powierzchni dowolnej figury płaskiej, określonej przez zbiór punktów.
Dla szerszej perspektywy i dokładniejszej oceny metody Monte Carlo, w swoim algorytmie liczę przybliżone pola figur takich jak:
- Koło o środku (5,5) i promieniu 4
- Prostokąt o wierzchołkach (2,2), (8,2), (8,6), (2,6)
- Trójkąt o wierzchołkach (1,3), (5,1), (9,3)
- Elipsa o środku (5,7), półosiach 2 i 4
- Nieregularny wielokąt o wierzchołkach (3,3), (5,7), (8,5), (7,3), (4,2)
Obliczenia wykonuję dla różnych wartości n (długość boku kwadratu KW) i k (liczba losowych punktów). Dodatkowo dla każdego przypadku punkty losowane są po 10 razy.
### Kod:
```matlab
% Wykonanie obliczeń dla różnych wartości n i k
nWartosci = [10, 20, 50, 100, 150]; % długość boku kwadratu KW
kWartosci = [100, 500, 1000, 5000, 10000]; % liczba losowanych punktów

% Definicja liczby prób
liczba_prob = 10;

% Koło o środku (5,5) i promieniu 4
srodek_kola = [5 5];
promien_kola = 4;

% Prostokąt o wierzchołkach (2,2), (8,2), (8,6), (2,6)
prostokat = [2 2; 8 2; 8 6; 2 6; 2 2];

% Trójkąt o wierzchołkach (1,3), (5,1), (9,3)
trojkat = [1 3; 5 1; 9 3; 1 3];

% Elipsa o środku (5,7), półosiach 2 i 4
srodek_elipsy = [5 7];
polos_elipsy = [2 4];

% Nieregularny wielokąt o wierzchołkach (3,3), (5,7), (8,5), (7,3), (4,2)
nieregularny_wielokat = [3 3; 5 7; 8 5; 7 3; 4 2; 3 3];

% Przechowywanie wyników
polaPowierzchniKola = zeros(length(nWartosci), length(kWartosci), liczba_prob);
polaPowierzchniProstokata = zeros(length(nWartosci), length(kWartosci), liczba_prob);
polaPowierzchniTrojkata = zeros(length(nWartosci), length(kWartosci), liczba_prob);
polaPowierzchniElipsy = zeros(length(nWartosci), length(kWartosci), liczba_prob);
polaPowierzchniWielokata = zeros(length(nWartosci), length(kWartosci), liczba_prob);

for i = 1:length(nWartosci)
	n = nWartosci(i);
	for j = 1:length(kWartosci)
		k = kWartosci(j);
		for p = 1:liczba_prob
			% Definicja obszaru pracy: kwadrat KW o boku n
			kw = [0 0; n 0; n n; 0 n; 0 0]; % współrzędne wierzchołków kwadratu KW
			% Losowanie punktów w obrębie kwadratu KW
			punkty = rand(k, 2) * n; % losowanie punktów w zakresie [0, n]
			% Obliczenie przybliżonego pola powierzchni koła
			punkty_wewnatrz_kola = sum((punkty(:,1)-srodek_kola(1)).^2 + 
			(punkty(:,2)-
			srodek_kola(2)).^2 <= promien_kola^2);
			polaPowierzchniKola(i, j, p) = (punkty_wewnatrz_kola / k) * (n^2);
			% Obliczenie przybliżonego pola powierzchni prostokąta
			punkty_wewnatrz_prostokata = sum(inpolygon(punkty(:,1), punkty(:,2), 
			prostokat(:,1), prostokat(:,2)));
			polaPowierzchniProstokata(i, j, p) = (punkty_wewnatrz_prostokata / k) 
			* (n^2);
			% Obliczenie przybliżonego pola powierzchni trójkąta
			punkty_wewnatrz_trojkata = sum(inpolygon(punkty(:,1), punkty(:,2), 
			trojkat(:,1), trojkat(:,2)));
			polaPowierzchniTrojkata(i, j, p) = (punkty_wewnatrz_trojkata / k) * 
			(n^2);
			% Obliczenie przybliżonego pola powierzchni elipsy
			elipsa = @(x,y) ((x-srodek_elipsy(1))/polos_elipsy(1)).^2 + ((y-
			srodek_elipsy(2))/polos_elipsy(2)).^2 <= 1;
			punkty_wewnatrz_elipsy = sum(elipsa(punkty(:,1), punkty(:,2)));
			polaPowierzchniElipsy(i, j, p) = (punkty_wewnatrz_elipsy / k) * (n^2);
			% Obliczenie przybliżonego pola powierzchni nieregularnego wielokąta
			punkty_wewnatrz_wielokata = sum(inpolygon(punkty(:,1), punkty(:,2), 
			nieregularny_wielokat(:,1), nieregularny_wielokat(:,2)));
			polaPowierzchniWielokata(i, j, p) = (punkty_wewnatrz_wielokata / k) * 
			(n^2);
		end
	end

	% Generowanie wykresu
	figure;
	hold on;
	% Rysowanie punktów
	plot(punkty(:,1), punkty(:,2), 'k.', 'DisplayName', 'Losowane punkty');
	% Rysowanie kwadratu KW
	plot(kw(:,1), kw(:,2), 'r-', 'LineWidth', 1, 'DisplayName', 'Kwadrat KW');
	% Rysowanie koła
	theta = linspace(0, 2*pi, 100);
	x_kola = srodek_kola(1) + promien_kola * cos(theta);
	y_kola = srodek_kola(2) + promien_kola * sin(theta);
	plot(x_kola, y_kola, 'b-', 'LineWidth', 3, 'DisplayName', 'Koło');
	% Rysowanie prostokąta
	plot(prostokat(:,1), prostokat(:,2), 'y-', 'LineWidth', 3, 'DisplayName', 
	'Prostokąt');
	% Rysowanie trójkąta
	plot(trojkat(:,1), trojkat(:,2), 'c-', 'LineWidth', 3, 'DisplayName', 
	'Trójkąt');
	% Rysowanie elipsy
	a = 4;
	b = 2;
	x0 = 5;
	y0 = 7;
	x = x0 + a * cos(theta);
	y = y0 + b * sin(theta);
	plot(x, y, 'g-', 'LineWidth', 3, 'DisplayName', 'Elipsa');
	% Rysowanie nieregularnego wielokąta
	plot(nieregularny_wielokat(:,1), nieregularny_wielokat(:,2), 'm-', 
	'LineWidth', 3, 'DisplayName', 'Nieregularny wielokąt');
	axis equal;
	xlabel('x');
	ylabel('y');
	title('Losowane punkty w kwadracie KW wraz z różnymi figurami płaskimi');
	legend('show');
end

% Analiza wyników
disp('Przybliżone pole powierzchni koła: ');
disp(polaPowierzchniKola);
disp('Przybliżone pole powierzchni prostokąta: ');
disp(polaPowierzchniProstokata);
disp('Przybliżone pole powierzchni trójkąta: ');
disp(polaPowierzchniTrojkata);
disp('Przybliżone pole powierzchni elipsy: ');
disp(polaPowierzchniElipsy);
disp('Przybliżone pole powierzchni nieregularnego wielokąta: ');
disp(polaPowierzchniWielokata);

% Definicja znanych pól figury
znane_pola = [pi*promien_kola^2; polyarea(prostokat(:,1), prostokat(:,2)); ...
polyarea(trojkat(:,1), trojkat(:,2)); pi*a*b; ...
polyarea(nieregularny_wielokat(:,1), nieregularny_wielokat(:,2))];

% Wykres punktowy z wynikami
figure;
% Koło
subplot(2, 3, 1);
hold on;
for i = 1:length(nWartosci)
	for j = 1:length(kWartosci)
		for p = 1:liczba_prob
			scatter(i, polaPowierzchniKola(i, j, p), 'filled');
		end
	end
end
xticks(1:length(nWartosci));
xticklabels(arrayfun(@(x) sprintf('n=%d', x), nWartosci, 'UniformOutput', false));
xlabel('Wartości n');
ylabel('Pole powierzchni');
title('Koło');
% Prostokąt
subplot(2, 3, 2);
hold on;
for i = 1:length(nWartosci)
	for j = 1:length(kWartosci)
		for p = 1:liczba_prob
			scatter(i, polaPowierzchniProstokata(i, j, p), 'filled');
		end
	end
end
xticks(1:length(nWartosci));
xticklabels(arrayfun(@(x) sprintf('n=%d', x), nWartosci, 'UniformOutput', false));
xlabel('Wartości n');
ylabel('Pole powierzchni');
title('Prostokąt');
% Trójkąt
subplot(2, 3, 3);
hold on;
for i = 1:length(nWartosci)
	for j = 1:length(kWartosci)
		for p = 1:liczba_prob
			scatter(i, polaPowierzchniTrojkata(i, j, p), 'filled');
		end
	end
end
xticks(1:length(nWartosci));
xticklabels(arrayfun(@(x) sprintf('n=%d', x), nWartosci, 'UniformOutput', false));
xlabel('Wartości n');
ylabel('Pole powierzchni');
title('Trójkąt');
% Elipsa
subplot(2, 3, 4);
hold on;
for i = 1:length(nWartosci)
	for j = 1:length(kWartosci)
		for p = 1:liczba_prob
			scatter(i, polaPowierzchniElipsy(i, j, p), 'filled');
		end
	end
end
xticks(1:length(nWartosci));
xticklabels(arrayfun(@(x) sprintf('n=%d', x), nWartosci, 'UniformOutput', false));
xlabel('Wartości n');
ylabel('Pole powierzchni');
title('Elipsa');
% Nieregularny wielokąt
subplot(2, 3, 5);
hold on;
for i = 1:length(nWartosci)
	for j = 1:length(kWartosci)
		for p = 1:liczba_prob
			scatter(i, polaPowierzchniWielokata(i, j, p), 'filled');
		end
	end
end
xticks(1:length(nWartosci));
xticklabels(arrayfun(@(x) sprintf('n=%d', x), nWartosci, 'UniformOutput', false));
xlabel('Wartości n');
ylabel('Pole powierzchni');
title('Nieregularny wielokąt');
sgtitle('Wyniki pól powierzchni dla różnych figur');
```

### Wykres 
Wykres przedstawia kwadrat KW o n=10 wraz z losowymi punktami (k=10000) oraz wybranymi figurami.

![[Pasted image 20240429133731.png]]

### Wyniki:
```matlab

Przybliżone pole powierzchni koła:  
  
(:,:,1) =  
47.0000 55.0000 55.9000 50.2000 49.8400  
52.0000 46.4000 45.2000 49.6800 46.6800  
25.0000 70.0000 60.0000 49.0000 46.2500  
100.0000 60.0000 140.0000 34.0000 56.0000  
0 0 0 45.0000 45.0000  
  
(:,:,2) =  
50.0000 51.2000 48.4000 50.0200 50.2100  
56.0000 43.2000 58.8000 50.2400 50.7200  
25.0000 50.0000 57.5000 54.5000 56.0000  
0 40.0000 70.0000 60.0000 28.0000  
0 180.0000 90.0000 85.5000 36.0000  
  
(:,:,3) =  
42.0000 46.6000 51.3000 50.4400 50.1900  
72.0000 52.0000 53.6000 51.2800 50.2000  
0 65.0000 40.0000 54.0000 51.0000  
0 20.0000 90.0000 54.0000 31.0000  
0 45.0000 67.5000 36.0000 51.7500  
  
(:,:,4) =  
51.0000 52.2000 49.6000 51.8200 50.1400  
64.0000 43.2000 39.2000 54.2400 50.6400  
25.0000 20.0000 40.0000 44.0000 49.5000  
0 40.0000 60.0000 56.0000 50.0000  
0 45.0000 45.0000 49.5000 63.0000  
  
(:,:,5) =  
48.0000 48.4000 49.1000 49.1000 49.9600  
56.0000 50.4000 45.2000 49.1200 49.5600  
75.0000 40.0000 42.5000 49.0000 46.2500  
100.0000 40.0000 30.0000 42.0000 50.0000  
225.0000 45.0000 22.5000 67.5000 51.7500  
  
(:,:,6) =  
47.0000 51.0000 51.7000 49.5400 50.0500  
44.0000 44.8000 45.6000 49.7600 49.8000  
25.0000 75.0000 50.0000 50.0000 54.2500  
200.0000 60.0000 30.0000 46.0000 43.0000  
225.0000 0 112.5000 36.0000 54.0000  
  
(:,:,7) =  
56.0000 48.4000 48.7000 49.6200 49.3200  
40.0000 48.8000 46.8000 50.4800 52.0800  
25.0000 60.0000 70.0000 47.0000 51.2500  
0 0 70.0000 54.0000 61.0000  
225.0000 45.0000 22.5000 58.5000 51.7500  
  
(:,:,8) =  
52.0000 50.2000 48.3000 50.8000 50.5900  
40.0000 58.4000 62.4000 48.9600 50.6000  
0 50.0000 32.5000 43.0000 47.5000  
300.0000 140.0000 60.0000 48.0000 47.0000  
0 45.0000 112.5000 54.0000 47.2500  
  
(:,:,9) =  
48.0000 51.8000 49.8000 50.4400 49.3900  
24.0000 55.2000 46.8000 49.9200 51.0800  
0 80.0000 47.5000 52.0000 49.7500  
0 20.0000 90.0000 50.0000 50.0000  
0 90.0000 112.5000 72.0000 51.7500  
  
(:,:,10) =  
52.0000 46.6000 52.0000 51.1200 49.5300  
36.0000 53.6000 43.6000 52.4000 51.2000  
75.0000 70.0000 60.0000 54.0000 43.7500  
0 40.0000 90.0000 64.0000 57.0000  
225.0000 135.0000 0 45.0000 33.7500  


Przybliżone pole powierzchni prostokąta:  
  
(:,:,1) =  
24.0000 23.8000 25.3000 24.0800 23.9200  
32.0000 25.6000 26.8000 24.2400 21.6000  
25.0000 30.0000 37.5000 23.0000 23.5000  
0 60.0000 70.0000 12.0000 26.0000  
0 0 0 31.5000 15.7500  
  
(:,:,2) =  
20.0000 25.0000 24.4000 23.6400 23.3300  
28.0000 16.0000 30.4000 24.4000 25.0000  
0 35.0000 22.5000 27.0000 30.5000  
0 0 30.0000 26.0000 14.0000  
0 135.0000 0 54.0000 22.5000  
  
(:,:,3) =  
19.0000 22.0000 25.0000 24.1800 23.7000  
48.0000 27.2000 26.4000 22.3200 25.2800  
0 35.0000 10.0000 26.5000 25.2500  
0 20.0000 50.0000 36.0000 11.0000  
0 45.0000 45.0000 18.0000 22.5000  

(:,:,4) =  
29.0000 25.0000 22.9000 24.3000 24.2800  
32.0000 18.4000 18.4000 26.0800 23.8400  
0 5.0000 12.5000 22.5000 26.0000  
0 40.0000 20.0000 22.0000 26.0000  
0 45.0000 0 9.0000 29.2500  
  
(:,:,5) =  
20.0000 23.4000 24.2000 23.5000 24.2700  
28.0000 21.6000 22.4000 26.3200 23.2800  
50.0000 10.0000 25.0000 22.5000 20.7500  
100.0000 40.0000 10.0000 12.0000 25.0000  
0 0 0 49.5000 20.2500  
  
(:,:,6) =  
20.0000 24.4000 25.5000 23.8400 24.3800  
16.0000 18.4000 18.0000 24.4000 24.3600  
0 35.0000 35.0000 22.0000 27.7500  
0 40.0000 0 24.0000 12.0000  
225.0000 0 67.5000 13.5000 29.2500  
  
(:,:,7) =  
25.0000 26.8000 20.9000 22.9400 24.2600  
32.0000 24.0000 20.0000 23.2000 23.6800  
0 35.0000 32.5000 20.0000 25.2500  
0 0 30.0000 26.0000 28.0000  
225.0000 0 0 31.5000 29.2500  
  
(:,:,8) =  
21.0000 24.4000 22.5000 24.9000 23.8100  
36.0000 23.2000 32.8000 21.6800 23.1200  
0 15.0000 20.0000 19.0000 22.2500  
0 100.0000 20.0000 26.0000 21.0000  
0 45.0000 22.5000 31.5000 15.7500  
  
(:,:,9) =  
20.0000 23.2000 22.7000 23.8400 23.4700  
16.0000 33.6000 19.2000 24.8800 24.7200  
0 30.0000 27.5000 25.0000 21.5000  
0 0 30.0000 22.0000 26.0000  
0 0 67.5000 31.5000 27.0000  
  
(:,:,10) =  
26.0000 23.6000 24.8000 23.7000 23.8900  
16.0000 27.2000 25.2000 25.4400 24.1600  
50.0000 30.0000 32.5000 24.5000 23.0000  
0 40.0000 50.0000 30.0000 28.0000  
225.0000 135.0000 0 22.5000 11.2500  


Przybliżone pole powierzchni trójkąta:  
  
(:,:,1) =  
9.0000 10.6000 9.1000 7.8200 8.0500  
4.0000 10.4000 7.2000 6.8000 7.0800  
0 15.0000 7.5000 10.5000 5.7500  
0 20.0000 30.0000 10.0000 7.0000  
0 0 0 9.0000 2.2500  
  
(:,:,2) =  
7.0000 6.4000 9.2000 8.0600 7.6600  
12.0000 4.8000 9.2000 6.9600 8.6400  
0 20.0000 2.5000 9.0000 9.7500  
0 0 0 10.0000 4.0000  
0 45.0000 0 18.0000 11.2500
  
(:,:,3) =  
8.0000 6.2000 6.4000 8.4400 7.5900  
28.0000 8.0000 5.2000 6.6400 8.4800  
0 15.0000 5.0000 12.5000 8.7500  
0 0 10.0000 10.0000 3.0000  
0 0 45.0000 4.5000 4.5000  
  
(:,:,4) =  
9.0000 8.0000 8.1000 8.2200 8.2700  
20.0000 7.2000 5.6000 7.8400 6.4400  
0 5.0000 7.5000 8.0000 9.2500  
0 0 10.0000 10.0000 6.0000  
0 0 0 9.0000 11.2500  
  
(:,:,5) =  
9.0000 8.0000 8.1000 7.6600 7.3400  
0 7.2000 8.0000 8.2400 8.0800  
0 5.0000 10.0000 7.0000 9.0000  
0 20.0000 0 4.0000 10.0000  
0 45.0000 0 13.5000 6.7500  
  
(:,:,6) =  
8.0000 6.8000 9.9000 8.3400 7.7000  
16.0000 7.2000 5.6000 9.1200 7.8800  
0 5.0000 10.0000 9.0000 7.7500  
200.0000 0 0 10.0000 1.0000  
225.0000 0 22.5000 0 15.7500  
  
(:,:,7) =  
9.0000 8.0000 7.6000 8.3000 8.6600  
8.0000 8.8000 6.8000 7.6800 8.4400  
0 5.0000 10.0000 9.0000 8.0000  
0 0 10.0000 10.0000 10.0000  
0 0 0 4.5000 0  
  
(:,:,8) =  
9.0000 8.4000 7.3000 8.4400 8.2600  
4.0000 12.0000 9.6000 7.4400 8.4000  
0 0 5.0000 7.0000 7.7500  
0 60.0000 20.0000 6.0000 5.0000  
0 0 0 9.0000 6.7500  
  
(:,:,9) =  
7.0000 7.2000 7.9000 8.2800 8.0000  
12.0000 8.8000 6.8000 6.5600 8.7600  
0 15.0000 7.5000 9.0000 9.5000  
0 0 0 12.0000 7.0000  
0 0 0 18.0000 6.7500  
  
(:,:,10) =  
8.0000 7.4000 8.1000 8.1400 7.7600  
8.0000 6.4000 7.6000 7.4400 7.7200  
25.0000 5.0000 10.0000 8.5000 6.5000  
0 20.0000 10.0000 6.0000 14.0000  
225.0000 45.0000 0 4.5000 6.7500  


Przybliżone pole powierzchni elipsy:  
  
(:,:,1) =  
22.0000 24.0000 24.2000 23.4200 23.1100  
28.0000 19.2000 27.2000 24.0000 23.1200  
25.0000 20.0000 40.0000 24.5000 23.7500  
100.0000 80.0000 30.0000 20.0000 25.0000  
0 0 22.5000 31.5000 24.7500  
  
(:,:,2) =  
26.0000 24.6000 22.5000 23.4200 23.0400  
24.0000 30.4000 33.2000 23.8400 25.1600  
25.0000 30.0000 35.0000 27.0000 26.0000  
0 20.0000 40.0000 26.0000 18.0000  
0 90.0000 45.0000 36.0000 15.7500  
  
(:,:,3) =  
22.0000 22.0000 24.5000 23.7800 22.5400  
24.0000 23.2000 30.4000 26.7200 25.6800  
0 15.0000 17.5000 29.0000 23.5000  
0 0 70.0000 24.0000 19.0000  
0 45.0000 0 22.5000 33.7500  
  
(:,:,4) =  
23.0000 25.4000 23.2000 23.3200 23.2500  
36.0000 16.8000 19.6000 26.8000 26.4000  
75.0000 20.0000 32.5000 19.5000 25.7500  
0 40.0000 20.0000 30.0000 27.0000  
0 45.0000 45.0000 13.5000 27.0000   
  
(:,:,5) =  
24.0000 24.4000 22.9000 22.7800 23.1200  
36.0000 24.0000 20.4000 24.6400 24.3600  
25.0000 25.0000 12.5000 26.0000 21.2500  
0 40.0000 20.0000 16.0000 21.0000  
0 0 0 45.0000 31.5000  
  
(:,:,6) =  
32.0000 23.2000 24.0000 23.0400 23.7100  
20.0000 24.8000 26.0000 21.8400 25.6400  
0 30.0000 20.0000 24.5000 26.7500  
0 40.0000 10.0000 24.0000 24.0000  
0 0 0 27.0000 15.7500   
  
(:,:,7) =  
26.0000 21.4000 24.2000 23.0400 22.5600  
16.0000 27.2000 22.0000 26.1600 26.6000  
25.0000 20.0000 42.5000 23.0000 25.0000  
0 0 40.0000 26.0000 33.0000  
0 0 0 36.0000 33.7500  
  
(:,:,8) =   
24.0000 25.8000 22.4000 23.2400 23.0600  
16.0000 30.4000 32.4000 24.0000 25.3200  
0 20.0000 17.5000 24.0000 26.0000  
300.0000 60.0000 40.0000 20.0000 27.0000  
0 45.0000 67.5000 22.5000 24.7500   
  
(:,:,9) =    
19.0000 26.6000 23.4000 24.2000 23.3100  
8.0000 21.6000 22.0000 26.0000 25.7200  
0 30.0000 15.0000 30.5000 20.7500  
0 0 60.0000 16.0000 36.0000  
0 45.0000 45.0000 27.0000 22.5000  
  
(:,:,10) =  
23.0000 23.0000 23.7000 23.7600 22.7300  
12.0000 28.8000 22.8000 27.6000 25.7200  
50.0000 25.0000 27.5000 28.5000 26.7500  
0 20.0000 50.0000 36.0000 26.0000  
0 45.0000 0 36.0000 13.5000  


Przybliżone pole powierzchni nieregularnego wielokąta:  
  
(:,:,1) =   
10.0000 14.4000 13.3000 14.1800 13.9200  
12.0000 14.4000 16.4000 15.4400 12.5200  
25.0000 15.0000 22.5000 15.5000 14.5000  
0 20.0000 30.0000 6.0000 9.0000  
0 0 0 22.5000 9.0000  
  
(:,:,2) =    
13.0000 14.0000 12.7000 14.0600 13.7000  
16.0000 10.4000 18.0000 14.4800 14.7600  
0 25.0000 17.5000 16.5000 16.5000  
0 0 30.0000 10.0000 7.0000  
0 45.0000 0 31.5000 15.7500  
  
(:,:,3) =  
13.0000 12.2000 14.8000 14.0400 13.4300  
32.0000 14.4000 18.8000 13.1200 15.0800  
0 10.0000 7.5000 16.0000 12.5000  
0 20.0000 50.0000 24.0000 8.0000  
0 45.0000 22.5000 9.0000 13.5000  
  
(:,:,4) =   
16.0000 14.2000 13.5000 14.3800 13.5400  
20.0000 8.0000 11.2000 15.9200 13.7600  
0 0 17.5000 12.5000 15.0000  
0 20.0000 10.0000 12.0000 15.0000  
0 0 0 0 20.2500
  
(:,:,5) =  
10.0000 12.4000 14.8000 13.7000 14.3800  
20.0000 12.0000 11.6000 15.4400 13.2800  
0 10.0000 17.5000 14.5000 10.5000  
0 20.0000 10.0000 6.0000 16.0000  
0 0 0 31.5000 15.7500  
  
(:,:,6) =   
17.0000 11.6000 15.5000 13.8200 14.5400  
8.0000 10.4000 11.2000 12.2400 14.8800  
0 20.0000 30.0000 11.0000 15.5000  
0 40.0000 0 10.0000 12.0000  
225.0000 0 22.5000 4.5000 4.5000  
  
(:,:,7) =  
14.0000 15.2000 12.8000 13.6200 14.0600  
16.0000 13.6000 12.0000 13.6800 13.7200  
0 30.0000 25.0000 11.0000 14.7500  
0 0 0 14.0000 19.0000  
0 0 0 27.0000 22.5000  
  
(:,:,8) =  
15.0000 15.2000 12.9000 14.4000 13.6600  
24.0000 16.0000 15.6000 12.3200 13.2000  
0 5.0000 10.0000 9.5000 13.0000  
0 20.0000 10.0000 12.0000 14.0000  
0 0 45.0000 22.5000 18.0000  
  
(:,:,9) =  
14.0000 12.0000 12.3000 13.9200 13.8600  
8.0000 15.2000 11.2000 15.8400 14.7200  
0 10.0000 12.5000 14.0000 13.0000  
0 0 30.0000 8.0000 16.0000  
0 0 45.0000 18.0000 20.2500  
  
(:,:,10) =  
12.0000 13.6000 14.2000 14.1200 13.9400  
8.0000 17.6000 13.6000 14.9600 14.5200  
25.0000 15.0000 7.5000 14.5000 14.0000  
0 20.0000 30.0000 16.0000 21.0000  
0 0 0 13.5000 6.7500
```


### Wykres z wynikami
Wykres pokazuje rozrzut wyników pól powierzchni dla konkretnych figur w zależności od długości boku (n) kwadratu KW.

![[Pasted image 20240429134852.png]]


#### Prawidłowe wartości pól:
- koło - 50.2655  
- prostokąt - 24.0000  
- trójkąt - 8.0000  
- elipsa - 25.1327  
- nieregularny wielokąt - 14.0000

### Wnioski
Jak widać większość wyników jest podobna do prawidłowych wartości pól powyższych figur. Możemy zauważyć, że dokładność jest tym większa, im krótszy jest bok n kwadratu oraz im więcej jest punktów losowych.