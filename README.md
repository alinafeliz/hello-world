public class Application {
    private CreditCalculator calculator;

    public void init() {
        calculator = new CreditCalculator();
    }

    public void run() {
        calculator.setAmount(500000);
        calculator.setYears(3);
        calculator.calculate();
        System.out.println("Месячный платеж: " + calculator.getMonthlyPayment());
    }

    public static void main(String[] args) {
        Application application = new Application();
        application.init();
        application.run();
    }
}
