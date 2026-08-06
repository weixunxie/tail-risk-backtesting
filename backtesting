clear
clc
close all
Stocks = {'SP500', 'CSI300'};
for kkk = 1:2
Underlying = Stocks{kkk}; % 'CSI300' or 'SP500'
filename = [Underlying, '.xlsx'];
data_SP500 = readtable(filename);
head(data_SP500);
SP500 = data_SP500.Close;
Date_SP500 = data_SP500.Date;
Returns_SP = tick2ret(SP500);
DateReturns_SP = Date_SP500(2:end);
SampleSize_SP = length(Returns_SP);
TestWindowStart_SP = find(year(DateReturns_SP)==2014,1);
TestWindow_SP = TestWindowStart_SP : SampleSize_SP;
EstimationWindowSize = TestWindowStart_SP-1;
pVaR_SP = [0.05 0.01];
pltdir = "./figs_" + Underlying + "/";
% plot output directory
%pltdir = "./figs_" + Underlying + "_" + alpha + "/";
if ~exist(pltdir,'dir')
mk_stat = mkdir(pltdir); % returns 1 if created; 0 on failure
end
%% Compute the VaR Using the Normal Distribution Method
Zscore_SP = norminv(pVaR_SP);
Normal95_SP = zeros(length(TestWindow_SP),1);
Normal99_SP = zeros(length(TestWindow_SP),1);
Normal_CVaR95_SP = zeros(length(TestWindow_SP), 1);
Normal_CVaR99_SP = zeros(length(TestWindow_SP), 1);
for t = TestWindow_SP
i = t - TestWindowStart_SP + 1;
EstimationWindow = t-EstimationWindowSize:t-1;
Sigma = std(Returns_SP(EstimationWindow));
Normal95_SP(i) = -Zscore_SP(1)*Sigma;
Normal99_SP(i) = -Zscore_SP(2)*Sigma;
Normal_CVaR95_SP(i) = mean(Returns_SP(Returns_SP >= Normal95_SP(i)));
Normal_CVaR99_SP(i) = mean(Returns_SP(Returns_SP >= Normal99_SP(i)));
end
figure(1);
set(gcf, 'Position', get(0, 'Screensize')); % Set figure to full screen
plot(DateReturns_SP(TestWindow_SP),[Normal95_SP Normal99_SP])
xlabel('Date','FontSize',24,'FontName','Times New Roman')
ylabel('VaR','FontSize',24,'FontName','Times New Roman')
legend({'95% Confidence Level','99% Confidence Level'},'Location','Best','FontSize',24,'FontName','Times New Roman')
if strcmp(Underlying, 'SP500')
title('S\&P 500 VaR Estimation Using the Normal Distribution Method','FontSize',24,'FontName','Times New Roman')
else
title('CSI 300 VaR Estimation Using the Normal Distribution Method','FontSize',24,'FontName','Times New Roman')
end
plt_fn = pltdir + "Normal_VaR.fig";
saveas(gcf,plt_fn);
plt_fn_2 = pltdir + "Normal_VaR.png";
saveas(gcf,plt_fn_2);
figure(2);
set(gcf, 'Position', get(0, 'Screensize')); % Set figure to full screen
plot(DateReturns_SP(TestWindow_SP),[Normal_CVaR95_SP Normal_CVaR99_SP])
xlabel('Date','FontSize',24,'FontName','Times New Roman')
ylabel('CVaR','FontSize',24,'FontName','Times New Roman')
legend({'95% Confidence Level','99% Confidence Level'},'Location','Best','FontSize',24,'FontName','Times New Roman')
if strcmp(Underlying, 'SP500')
title('S\&P 500 CVaR Estimation Using the Normal Distribution Method','FontSize',24,'FontName','Times New Roman')
else
title('CSI 300 CVaR Estimation Using the Normal Distribution Method','FontSize',24,'FontName','Times New Roman')
end
plt_fn = pltdir + "Normal_CVaR.fig";
saveas(gcf,plt_fn);
plt_fn_2 = pltdir + "Normal_CVaR.png";
saveas(gcf,plt_fn_2);
%% Compute the VaR Using the Historical Simulation Method
Historical95_SP = zeros(length(TestWindow_SP),1);
Historical99_SP = zeros(length(TestWindow_SP),1);
Historical_CVaR95_SP = zeros(length(TestWindow_SP), 1);
Historical_CVaR99_SP = zeros(length(TestWindow_SP), 1);
for t = TestWindow_SP
i = t - TestWindowStart_SP + 1;
EstimationWindow = t-EstimationWindowSize:t-1;
X = Returns_SP(EstimationWindow);
Historical95_SP(i) = -quantile(X,pVaR_SP(1));
Historical99_SP(i) = -quantile(X,pVaR_SP(2));
Historical_CVaR95_SP(i) = mean(Returns_SP(Returns_SP >= Historical95_SP(i)));
Historical_CVaR99_SP(i) = mean(Returns_SP(Returns_SP >= Historical99_SP(i)));
end
figure(3);
set(gcf, 'Position', get(0, 'Screensize')); % Set figure to full screen
plot(DateReturns_SP(TestWindow_SP),[Historical95_SP Historical99_SP])
ylabel('VaR','FontSize',24,'FontName','Times New Roman')
xlabel('Date','FontSize',24,'FontName','Times New Roman')
legend({'95% Confidence Level','99% Confidence Level'},'Location','Best','FontSize',24,'FontName','Times New Roman')
if strcmp(Underlying, 'SP500')
title('S\&P 500 VaR Estimation Using the Historical Simulation Method','FontSize',24,'FontName','Times New Roman')
else
title('CSI 300 VaR Estimation Using the Historical Simulation Method','FontSize',24,'FontName','Times New Roman')
end
plt_fn = pltdir + "Historical_VaR.fig";
saveas(gcf,plt_fn);
plt_fn_2 = pltdir + "Historical_VaR.png";
saveas(gcf,plt_fn_2);
figure(4);
set(gcf, 'Position', get(0, 'Screensize')); % Set figure to full screen
plot(DateReturns_SP(TestWindow_SP),[Historical_CVaR95_SP Historical_CVaR99_SP])
ylabel('CVaR','FontSize',24,'FontName','Times New Roman')
xlabel('Date','FontSize',24,'FontName','Times New Roman')
legend({'95% Confidence Level','99% Confidence Level'},'Location','Best','FontSize',24,'FontName','Times New Roman')
if strcmp(Underlying, 'SP500')
title('S\&P 500 CVaR Estimation Using the Historical Simulation Method','FontSize',24,'FontName','Times New Roman')
else
title('CSI 300 CVaR Estimation Using the Historical Simulation Method','FontSize',24,'FontName','Times New Roman')
end
plt_fn = pltdir + "Historical_CVaR.fig";
saveas(gcf,plt_fn);
plt_fn_2 = pltdir + "Historical_CVaR.png";
saveas(gcf,plt_fn_2);
%% Compute the VaR Using the Exponential Weighted Moving Average Method (EWMA)
Lambda_SP = 0.94;
Sigma2_SP = zeros(length(Returns_SP),1);
Sigma2_SP(1) = Returns_SP(1)^2;
for i = 2 : (TestWindowStart_SP-1)
Sigma2_SP(i) = (1-Lambda_SP) * Returns_SP(i-1)^2 + Lambda_SP * Sigma2_SP(i-1);
end
Zscore_SP = norminv(pVaR_SP);
EWMA95_SP = zeros(length(TestWindow_SP),1);
EWMA99_SP = zeros(length(TestWindow_SP),1);
EWMA_CVaR_95_SP = zeros(length(TestWindow_SP),1);
EWMA_CVaR_99_SP = zeros(length(TestWindow_SP),1);
for t = TestWindow_SP
k = t - TestWindowStart_SP + 1;
Sigma2_SP(t) = (1-Lambda_SP) * Returns_SP(t-1)^2 + Lambda_SP * Sigma2_SP(t-1);
Sigma = sqrt(Sigma2_SP(t));
EWMA95_SP(k) = -Zscore_SP(1)*Sigma;
EWMA99_SP(k) = -Zscore_SP(2)*Sigma;
EWMA_CVaR_95_SP(k) = mean(Returns_SP(Returns_SP >= EWMA95_SP(k)));
EWMA_CVaR_99_SP(k) = mean(Returns_SP(Returns_SP >= EWMA99_SP(k)));
end
figure(5);
set(gcf, 'Position', get(0, 'Screensize')); % Set figure to full screen
plot(DateReturns_SP(TestWindow_SP),[EWMA95_SP EWMA99_SP])
ylabel('VaR','FontSize',24,'FontName','Times New Roman')
xlabel('Date','FontSize',24,'FontName','Times New Roman')
legend({'95% Confidence Level','99% Confidence Level'},'Location','Best','FontSize',24,'FontName','Times New Roman')
if strcmp(Underlying, 'SP500')
title('S\&P 500 VaR Estimation Using the EWMA Method','FontSize',24,'FontName','Times New Roman')
else
title('CSI 300 VaR Estimation Using the EWMA Method','FontSize',24,'FontName','Times New Roman')
end
plt_fn = pltdir + "EWMA_VaR.fig";
saveas(gcf,plt_fn);
plt_fn_2 = pltdir + "EWMA_VaR.png";
saveas(gcf,plt_fn_2);
figure(6);
set(gcf, 'Position', get(0, 'Screensize')); % Set figure to full screen
plot(DateReturns_SP(TestWindow_SP),[EWMA_CVaR_95_SP EWMA_CVaR_99_SP])
ylabel('CVaR','FontSize',24,'FontName','Times New Roman')
xlabel('Date','FontSize',24,'FontName','Times New Roman')
legend({'95% Confidence Level','99% Confidence Level'},'Location','Best','FontSize',24,'FontName','Times New Roman')
if strcmp(Underlying, 'SP500')
title('S\&P 500 CVaR Estimation Using the EWMA Method','FontSize',24,'FontName','Times New Roman')
else
title('CSI 300 CVaR Estimation Using the EWMA Method','FontSize',24,'FontName','Times New Roman')
end
plt_fn = pltdir + "EWMA_CVaR.fig";
saveas(gcf,plt_fn);
plt_fn_2 = pltdir + "EWMA_CVaR.png";
saveas(gcf,plt_fn_2);
%% VaR Backtesting
ReturnsTest = Returns_SP(TestWindow_SP);
DatesTest = DateReturns_SP(TestWindow_SP);
figure(7);
set(gcf, 'Position', get(0, 'Screensize')); % Set figure to full screen
plot(DatesTest,[ReturnsTest -Normal95_SP -Historical95_SP -EWMA95_SP])
ylabel('VaR','FontSize',24,'FontName','Times New Roman')
xlabel('Date','FontSize',24,'FontName','Times New Roman')
legend({'Returns','Normal','Historical','EWMA'},'Location','Best','FontSize',24,'FontName','Times New Roman')
if strcmp(Underlying, 'SP500')
title('Comparison of returns and VaR at 95% for different models for S\&P 500','FontSize',24,'FontName','Times New Roman')
else
title('Comparison of returns and VaR at 95% for different models for CSI 300','FontSize',24,'FontName','Times New Roman')
end
plt_fn = pltdir + "VaR_95_Backtesting.fig";
saveas(gcf,plt_fn);
plt_fn_2 = pltdir + "VaR_95_Backtesting.png";
saveas(gcf,plt_fn_2);
% plot output directory
figure(8);
set(gcf, 'Position', get(0, 'Screensize')); % Set figure to full screen
plot(DatesTest,[ReturnsTest -Normal99_SP -Historical99_SP -EWMA99_SP])
ylabel('VaR','FontSize',24,'FontName','Times New Roman')
xlabel('Date','FontSize',24,'FontName','Times New Roman')
legend({'Returns','Normal','Historical','EWMA'},'Location','Best','FontSize',24,'FontName','Times New Roman')
if strcmp(Underlying, 'SP500')
title('Comparison of returns and VaR at 99% for different models for S\&P 500','FontSize',24,'FontName','Times New Roman')
else
title('Comparison of returns and VaR at 99% for different models for CSI 300','FontSize',24,'FontName','Times New Roman')
end
plt_fn = pltdir + "VaR_99_Backtesting.fig";
saveas(gcf,plt_fn);
plt_fn_2 = pltdir + "VaR_99_Backtesting.png";
saveas(gcf,plt_fn_2);
figure(9);
set(gcf, 'Position', get(0, 'Screensize')); % Set figure to full screen
plot(DatesTest,[ReturnsTest -Normal_CVaR95_SP -Historical_CVaR95_SP -EWMA_CVaR_95_SP])
ylabel('CVaR','FontSize',24,'FontName','Times New Roman')
xlabel('Date','FontSize',24,'FontName','Times New Roman')
legend({'Returns','Normal','Historical','EWMA'},'Location','Best','FontSize',24,'FontName','Times New Roman')
if strcmp(Underlying, 'SP500')
title('Comparison of returns and CVaR at 95% for different models for S\&P 500','FontSize',24,'FontName','Times New Roman')
else
title('Comparison of returns and CVaR at 95% for different models for CSI 300','FontSize',24,'FontName','Times New Roman')
end
plt_fn = pltdir + "CVaR_95_Backtesting.fig";
saveas(gcf,plt_fn);
plt_fn_2 = pltdir + "CVaR_95_Backtesting.png";
saveas(gcf,plt_fn_2);
% plot output directory
figure(10);
set(gcf, 'Position', get(0, 'Screensize')); % Set figure to full screen
plot(DatesTest,[ReturnsTest -Normal_CVaR99_SP -Historical_CVaR99_SP -EWMA_CVaR_99_SP])
ylabel('CVaR','FontSize',24,'FontName','Times New Roman')
xlabel('Date','FontSize',24,'FontName','Times New Roman')
legend({'Returns','Normal','Historical','EWMA'},'Location','Best','FontSize',24,'FontName','Times New Roman')
if strcmp(Underlying, 'SP500')
title('Comparison of returns and CVaR at 99% for different models for S\&P 500','FontSize',24,'FontName','Times New Roman')
else
title('Comparison of returns and CVaR at 99% for different models for CSI 300','FontSize',24,'FontName','Times New Roman')
end
plt_fn = pltdir + "CVaR_99_Backtesting.fig";
saveas(gcf,plt_fn);
plt_fn_2 = pltdir + "CVaR_99_Backtesting.png";
saveas(gcf,plt_fn_2);
%% VaR Backtesting Result
vbt_SP = varbacktest(ReturnsTest,[Normal95_SP Historical95_SP EWMA95_SP Normal99_SP Historical99_SP ...
EWMA99_SP],'PortfolioID','S&P','VaRID',{'Normal95','Historical95','EWMA95',...
'Normal99','Historical99','EWMA99'},'VaRLevel',[0.95 0.95 0.95 0.99 0.99 0.99]);
Vbt_SP_rtn = runtests(vbt_SP);
file_path = pltdir+ 'VaR_result.xlsx';
writetable(Vbt_SP_rtn, file_path);
cvbt_SP = varbacktest(ReturnsTest,[Normal_CVaR95_SP Historical_CVaR95_SP EWMA_CVaR_95_SP Normal_CVaR99_SP Historical_CVaR99_SP ...
EWMA_CVaR_99_SP],'PortfolioID','S&P','VaRID',{'Normal95','Historical95','EWMA95',...
'Normal99','Historical99','EWMA99'},'VaRLevel',[0.95 0.95 0.95 0.99 0.99 0.99]);
CVbt_SP_rtn = runtests(cvbt_SP);
file_path_c = pltdir+ 'CVaR_result.xlsx';
writetable(CVbt_SP_rtn, file_path_c);
end
