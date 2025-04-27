IEEE-CIS Fraud Detection

ამ ამოცანაში მიზანი იყო, ამოგვეცნო გაყალბებული ტრანზაქციები IEEE-CIS Fraud Detection მონაცემთა ნაკრებიდან. ამოცანის ფარგლებში გავაკეთე cleaning, feature engineering, feature selection, model training.

experiment [Random_Forest](https://dagshub.com/asurm22/ML2.mlflow/#/experiments/2?viewStateShareKey=3894e7dac091113a949e1a0b144bdfbf23f857b1cfb2b6251e919052fe25b155&compareRunsMode=TABLE)

პირველი მოდელად ვცადე Random Forest მხოლოდ train_transaction dataset-ზე.
თავდაპირველად მარტივად დავიწყე მონაცემების გაწმენდით: დავდროპე სვეტები, სადაც >90% იყო ნალი. ასევე მივმართე feature engineering-ს და მისინგები შევავსე მოდით ან მედიანით. კატეგორიულ სვეტებზე გამოვიყენე Target Encoder. შემდეგ ასევე ამ ეტაპზე გავაერთიანე train_transaction და train_identity დატასეტები.
ტრენინგზე: თავდაპირველ ეტაპებზე აშკარა იყო ოვერფიტინგი.
შემდეგი რანები ("reduced complexity და correlation filter"):
მოდელის კომპლექსურობა შევამცირე: ნაკლები ხეები (n_estimators), მაქსიმალური სიღრმე (max_depth), და გავზარდე min_samples_leaf. დავამატე კორელაციის ფიჩერების ფილტრაცია, რომ მოდელს ნაკლები ზედმეტი ფიჩერი ჰქონოდა. 95%ზე მეტ კორელაციის მქონე ფიჩერებიდან ერთერთს ვდროპავდი.
შედეგი: ოვერფიტინგი შემცირდა, ტრენინგი და ვალიდაცია უფრო ახლოს მივიდა.

experiment [XGBoost](https://dagshub.com/asurm22/ML2.mlflow/#/experiments/1?viewStateShareKey=60b5838b3c07b10d688fd52b4dd6c37593b139dcfb12d21877e12fcb552682f6&compareRunsMode=TABLE)

შემდეგ გადავედი XGBoost-ზე.
პირველი რანი cleaning და feature engineering-ში იგივე გზას მივმართე:
n_estimators=100, max_depth=6, learning_rate=0.1.
ტრენინგზე: გარკვეული ოვერფიტინგი მაინც ჩანდა.
ამიტომაც შემდეგ რანებზე hypermarametres-შევცვალე, სხვადასხვა ვცადე.
n_estimators გავზარდე 1000-მდე უკეთესი სტაბილურობისთვის, subsample ოდნავ გავზარდე, learning_rate შევამცირე.
შედეგი: ტრენინგი და ვალიდაცია უფრო დაბალანსდა. საბოლოოდ კაი შედეგი დადო.


experiment [Logistic_Regression](https://dagshub.com/asurm22/ML2.mlflow/#/experiments/3?viewStateShareKey=3894e7dac091113a949e1a0b144bdfbf23f857b1cfb2b6251e919052fe25b155&compareRunsMode=TABLE)

ამის შემდეგ გადავედი ლოჯისტიკურ რეგრესიაზე — უფრო მარტივი მოდელი.
გამოვიყენე LogisticRegression ამ პარამეტრებით:
LogisticRegression(
    solver='saga',
    max_iter=1000,
    penalty='l2',
    C=1.0,
    random_state=42,
    n_jobs=-1
)
ძალიან ნელი იყო, შემდეგ შევცვალე max_iter 300-ზე.
Feature Engineering იგივე დავტოვე.
ამან მომცა ყველაზე ცუდი შედეგი.

experiment [LightGBM](https://dagshub.com/asurm22/ML2.mlflow/#/experiments/0?viewStateShareKey=60b5838b3c07b10d688fd52b4dd6c37593b139dcfb12d21877e12fcb552682f6&compareRunsMode=TABLE)

ბოლოს დავტესტე LightGBM, რაც აღმოჩნდა საუკეთესო მოდელი.
პირველი რანი ("lightgbm basic run"):
გამოყენებული პარამეტრები:
LGBMClassifier(
    n_estimators=1000,
    learning_rate=0.05,
    num_leaves=64,
    max_depth=-1,
    n_jobs=-1,
    random_state=42
)
Feature Engineerin იგივე დავტოვე
ვცადე როგორც Feature Selection ით ისევე Feature Selection-ის გარეშე. Feature Selection-მა საგრძნობლად შეანელა და შედეგი დიდად არშეუცვლია.
ტრენინგზე და ვალიდაციაზე საკმაოდ ახლო სქორები იყო.

Overfitting Notes:

ოვერფიტინგი ვამჩნევდი, როცა:
ტრენინგის სქორი ძალიან მაღალი იყო და ვალიდაციის სქორი აშკარად დაბალი.
Random Forest-ში და XGBoost-ის პირველ რანებში აშკარად ჩანდა ეს განსხვავება.
LightGBM-ში სქორები ბევრად უფრო ახლო იყო.

ოვერფიტინგის შესამცირებლად:
შევამცირე მოდელის კომპლექსურობა (max_depth, min_samples_leaf), ფიჩერების ფილტრაცია.

საბოლოოდ XGBoost და LightGBM დაახლოებით ერთნაირი შედეგები ქონდათ თუმცა LightGBM ავირჩიე როგორც ყველაზე ბალანსირებული და ძლიერი მოდელი, რომელიც ოვერფიტინგს ყველაზე კარგად გაუმკლავდა.
