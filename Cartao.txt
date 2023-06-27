import java.time.LocalDate;
import java.util.Random;

public class Cartao {
    private String numCartao;
    private String dataVal = "";
	private String codSeg;

	private float limite;
	private float saldo;

	private float pagamento;
	private int id;
	private boolean dep;

    public Cartao(){

    }
    
    public Cartao(String numCartao,String validade,String codSeg,
    		float limite,float saldo,float pagamento,boolean dep,int id){

    }

    public Cartao(float saldo,float limite,boolean dep){
		this.saldo = saldo;
		this.limite = limite;
		this.dep = dep;
		geraNumCartao();
		geraDataVal();
		geraCodSeg();
    }

    public void geraDataVal(){
		Random r = new Random();
		int min = (int) LocalDate.of(2024, 1, 1).toEpochDay();
		int max = (int) LocalDate.of(2040, 1, 1).toEpochDay();
		long randomDay = min + r.nextInt(max - min);

		LocalDate d = LocalDate.ofEpochDay(randomDay);
		dataVal += d.getMonthValue()+"/"+d.getYear();
	}

	public float getSaldoTotal(){
		return saldo+limite;
	}
	
	public void geraNumCartao() {
		Random n = new Random();
		numCartao = "";
		int a = 0;
		Cartao c = new Cartao();
		CartaoDAO cd = new CartaoDAO();
		do{
			for (int i = 0; i < 16; i++) {
				a++;
				if(a == 4 && i< 15){
					numCartao += n.nextInt(9)+".";
					a = 0;
				}else{
					numCartao += n.nextInt(9);
				}
			}
			c = cd.consultarCartaoN(numCartao);
		}while(numCartao != c.getNumCartao());
	}

	public void geraCodSeg() {
		Random n = new Random();
		codSeg = "";
		for (int i = 0; i < 3; i++) {
			codSeg += n.nextInt(9);
	    }
	}

	public boolean isDep() {
		return dep;
	}

	public void setDep(boolean dep) {
		this.dep = dep;
	}

	public float getPagamento() {
		return pagamento;
	}

	public void pagarFatura(float v) {
		if(v <=  limite){
			limite -= v;
		}else if(limite > 0){
			float a = v-limite;
			limite = 0;
			saldo -= a;
			pagamento -= v;
		}else{
			saldo -= v;
		}
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public void transacao(float v){
		pagamento += v;
		limite -= v;
	}

	public String getNumCartao(){
		return numCartao;
	}

	public String getDataVal(){
		return dataVal;
	}

	public float getLimite(){
		return limite;
	}

	public float getSaldo(){
		return saldo;
	}

	public String getCodSeg() {
		return codSeg;
	}

	public void setNumCartao(String numCartao) {
		this.numCartao = numCartao;
	}

	public void setDataVal(String dataVal) {
		this.dataVal = dataVal;
	}

	public void setCodSeg(String codSeg) {
		this.codSeg = codSeg;
	}

	public void setLimite(float limite) {
		this.limite = limite;
	}

	public void setSaldo(float saldo) {
		this.saldo = saldo;
	}

	public void setPagamento(float pagamento) {
		this.pagamento = pagamento;
	}


}
