import java.util.ArrayList;

public class Cliente extends Banco{
    private String nome;
    private String cpf;
    private boolean dep;

    private Cliente titular;

    private Cliente dependente;

    private ArrayList<Cartao> cartoes = new ArrayList<>();

    public Cliente(){

    }

    public Cliente(String nome, String cpf){
        this.nome = nome;
        this.cpf = cpf;
    }

    public Cliente(String nome, String cpf,String cpfTit){
        this.nome = nome;
        this.cpf = cpf;
        dep = true;
        titular = consultarPCPF(cpfTit);
        titular.setDependente(this);
    }

    
    public Cliente getDependente() {
        return dependente;
    }

    public void setDependente(Cliente dependente) {
        this.dependente = dependente;
    }

    //===========================================
    public Cliente getTitular() {
        return titular;
    }

    public void setTitular(Cliente titular) {
        this.titular = titular;
    }

    //===========================================
    public void addCartao(Cartao cartao){
        cartoes.add(cartao);
    }

    public ArrayList<Cartao> getCartoes(){
        return cartoes;
    }

    public boolean dep(){
        return dep;
    }

    public String getNome() {
        return nome;
    }
    public void setNome(String nome) {
        this.nome = nome;
    }

    //===========================================
    public String getCpf() {
        return cpf;
    }
    public void setCpf(String cpf) {
        this.cpf = cpf;
    }

    //===========================================
    public boolean isDep() {
        return dep;
    }
    public void setDep(boolean dep) {
        this.dep = dep;
    }

}
