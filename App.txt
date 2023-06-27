import java.util.ArrayList;
import java.util.Scanner;

public class App{
    public static Cliente cliente;

    public static Scanner sc(String msg){
        System.out.println(msg);
        Scanner sc = new Scanner(System.in);
        return sc;
    }

    public static int menu(){
        System.out.println("---------------------------------\nBanco do Brasil 2.0\n---------------------------------");
        System.out.println("1 - Cadastrar cliente\n2 - Emitir cartão\n3 - Realizar transação\n4 - Pagar fatura\n5 - Consultar saldo\n6 - Consultar clientes\n7 - Consultar dados do cartão\n8 - Sair");
        return sc("Digite a opção: ").nextInt();
    }
    //===========================CADASTRA CLIENTE===================================   
    public static String cadastraCliente(){
        System.out.println("---------------------------------");
        String nome = sc("Digite o nome: ").next();
        String cpf;
        do{
            cpf = sc("Digite o CPF: ").next();
            if(consultarPCPF(cpf) != null){
                System.out.println("Esse cpf já existe.");
            }
        }while(consultarPCPF(cpf) != null);
        ClienteDAO cd = new ClienteDAO();
        Cliente cliente = new Cliente();
        cliente.setNome(nome);
        cliente.setCpf(cpf);
        cd.inserir(cliente);
        //cadastrarCliente(nome,cpf);
        return cpf;
    }

    //===========================EMITE CARTAO===================================
    public static void emiteCartao(){
        CartaoDAO cd = new CartaoDAO();
        String cpf ="";
        String cpfD = "";
        Cliente titular = null;
        Cliente dependente = null;
        System.out.println("---------------------------------");
        System.out.println("Emitir cartão.");
        System.out.println("---------------------------------");
        do{
            cpf = sc("Digite o CPF:\nDigite 0 para cancelar").next();
            if(consultarPCPF(cpf) == null){
                System.out.println("Cliente não encontrado.");
            }else{
                titular = consultarPCPF(cpf);
            }
        }while(titular == null && !cpf.equals("0"));
        int op = 0;
        int i = 0;
        Cliente cli = titular;
        Cartao cartao = null;
        boolean tCT = true; 
        if(titular != null)
        if(titular.isDep()){
            do{
                if(cd.consultarCartao(cpf).size()  > 0)
                cartao = cli.getCartoes().get(i);
                i++;
                if(cartao.isDep() == true){
                    tCT = false;
                }else{
                    tCT = true;
                }
            }while(cd.consultarCartao(cpf).size() -1 > i);
        }
        //System.out.println(tCT);
        
        if(cd.consultarCartao(cpf).size() <= 0 || tCT == false){
            emissao(dependente,titular,false);
        }else if(cd.consultarCartao(cpf).size() > 0 && tCT == true){
            do{
                System.out.println("---------------------------------");
                System.out.println("Conta: "+titular.getNome());
                System.out.println("Cartão adicional:\n1 - Titular da conta\n2 - Outra pessoa\n3 - Cancelar");
                op = sc("Digite a opção: ").nextInt();
                if(op == 1){
                    emissao(dependente,titular,true);
                    dependente = titular;
                }else if(op == 2){
                    cpfD = sc("Digite o CPF: ").next();
                    if(consultarPCPF(cpfD) == null){
                        System.out.println("Cliente não encontrado.");
                    }else if(cpfD == cpf){
                        System.out.println("CPF igual da conta");
                    }else{
                        dependente = consultarPCPF(cpfD);
                        emissao(dependente,titular,true);
                    }
                }else if(op != 3){
                    System.out.println("Opção inválida.");
                }
            }while(dependente == null || op < 1 && op > 3);
        }

    }
    //===============================EMISSAO CARTÃO=======================================
    public static void emissao(Cliente dependente, Cliente titular,boolean dep){
        CartaoDAO cd = new CartaoDAO();
        float saldo;
        do{
            saldo = sc("Saldo: ").nextFloat();
        }while(saldo <= 0);
        float limite; 
        float limitePrincipal = 0;
        int i = 0;
        Cartao cartao = null;
        if(dependente != null || dep){
            do{
                cartao = cd.consultarCartao(titular.getCpf()).get(i);
                i++;
            }while(cartao.isDep());
            limitePrincipal = cartao.getLimite();
        }

        do{
            limite = sc("Limite: ").nextFloat();
            if(limite > saldo){
                System.out.println("---------------------------------");
                System.out.println("Valor do limite deve ser menor ou igual ao valor do saldo: "+saldo);
            }else if((dependente != null || dep) && limite > limitePrincipal){
                System.out.println("---------------------------------");
                System.out.println("Valor do limite não pode ser maior ou igual ao limite do cartão principal: "+limitePrincipal);      
            }
        }while(limite > saldo || ((dependente != null || dep) && limite > limitePrincipal) || limite <= 0);
        emitir(dependente,titular,saldo,limite,dep);
        
    }
    //===========================EMITIR======================================
    public static void emitir(Cliente dependente,Cliente titular, float saldo, float limite, boolean dep){
        Cartao cart = new Cartao(saldo,limite,dep);
        CartaoDAO cartao = new CartaoDAO();
        ClienteDAO cd = new ClienteDAO();
        if(dependente != null){
        	System.out.println("dep != null");
            dependente.setTitular(titular);
            dependente.setDep(true);
            cd.setDep(dependente.getCpf());
            titular.setDependente(dependente);
            cartao.inserir(cart,dependente.getCpf());
        }else{
        	System.out.println("dep == null");
            cartao.inserir(cart,titular.getCpf());
        }
        
        System.out.println("---------------------------------");
        System.out.println("Cartão emitido com sucesso.");
        System.out.println("---------------------------------");

    }
    //===========================TRANSAÇÃO===================================
    public static void realizaTransacao(){
    	CartaoDAO cd = new CartaoDAO();
        String cpf = sc("Digite o CPF: ").next();
        if(consultarPCPF(cpf)!= null){
            Cliente cli = consultarPCPF(cpf);
            if(cd.consultarCartao(cpf).size() <= 0){
                System.out.println("---------------------------------");
                System.out.println("Este cliente não tem cartão.");
            }else{
                int i = -1;
                for(Cartao ca: cd.consultarCartao(cpf)){
                    i++;
                    System.out.println("---------------------------------");
                    System.out.println(i+" - Para escolher esse cartão.");
                    System.out.println("Número cartão: "+ca.getNumCartao());
                    System.out.println("---------------------------------");
                }
                int op;
                do{
                    op = sc("Digite a opção do cartão: ").nextInt();
                    if(op < 0 || op > cd.consultarCartao(cpf).size()){
                        System.out.println("Opção inválida.");
                    }
                }while(op < 0 && op > cd.consultarCartao(cpf).size());
                String codSeg;
                String dataVal;
                do{
                    codSeg = sc("Digite o código de segurança: ").next();
                    dataVal = sc("Digite a validade: ").next();
                    if(!codSeg.equals(cd.consultarCartao(cpf).get(op).getCodSeg())){
                        System.out.println("Código de segurança inválido.");
                    }
                    if(!dataVal.equals(cd.consultarCartao(cpf).get(op).getDataVal())){
                        System.out.println("Data de validade inválida.");
                    }
                }while(!codSeg.equals(cd.consultarCartao(cpf).get(op).getCodSeg()) || 
                		!dataVal.equals(cd.consultarCartao(cpf).get(op).getDataVal()));
                float v;
                do{
                    System.out.println("Limite disponivel: "+cd.consultarCartao(cpf).get(op).getLimite());
                    v = sc("Valor da transação: ").nextFloat();
                    if(v > cd.consultarCartao(cpf).get(op).getLimite()){
                        System.out.println("Limite insuficiente.");
                    }
                }while(v > cd.consultarCartao(cpf).get(op).getLimite());
                
                Cartao cartao = cd.consultarCartao(cpf).get(op);
                cd.transacao(v, cpf, cartao);
                System.out.println("---------------------------------");
                System.out.println("Transação realizada com sucesso.");
                //realizarTransacao(sc("Nome do estabelecimento: ").next(),v,cli,op);
            }
        }else{
            System.out.println("---------------------------------");
            System.out.println("Cliente não existe.");
        }
    }
    //===============================FATURA=======================================
    public static void pagaFatura(){
        CartaoDAO cd = new CartaoDAO();
        String cpf = sc("Digite o CPF: ").next();
        if(consultarPCPF(cpf)!= null){
            if(cd.consultarCartao(cpf).size() == 0){
                System.out.println("---------------------------------");
                System.out.println("Este cliente não tem cartão.");
            }else{
                int i = -1;
                for(Cartao ca: cd.consultarCartao(cpf)){
                    i++;
                    System.out.println("---------------------------------");
                    System.out.println(i+" - Para escolher esse cartão.");
                    System.out.println("Número cartão: "+ca.getNumCartao());
                    System.out.println("---------------------------------");
                }
                int op;
                do{
                    op = sc("Digite a opção do cartão: ").nextInt();
                    if(op < 0 || op > cd.consultarCartao(cpf).size()){
                        System.out.println("Opção inválida.");
                    }
                }while(op < 0 && op > cd.consultarCartao(cpf).size());  
                Cartao cartao = cd.consultarCartao(cpf).get(op);
                cd.pagarFat(cpf, cartao);
                System.out.println("---------------------------------");
                System.out.println("Transação realizada com sucesso.");
            }

        }else{
            System.out.println("---------------------------------");
            System.out.println("Cliente não existe.");
        }

    }
    //===========================CONSULTA SALDO===================================
    public static void consultaSaldo(){
        CartaoDAO cd = new CartaoDAO();
        String cpf = sc("Digite o CPF: ").next();
        if(consultarPCPF(cpf)!= null){
            Cliente cli = consultarPCPF(cpf);
            if(cd.consultarCartao(cpf).size() == 0){
                System.out.println("---------------------------------");
                System.out.println("Este cliente não tem cartão.");
            }else{
                int i = -1;
                for(Cartao ca: cd.consultarCartao(cpf)){
                    i++;
                    System.out.println("---------------------------------");
                    System.out.println(i+" - Para escolher esse cartão.");
                    System.out.println("Número cartão: "+ca.getNumCartao());
                    System.out.println("---------------------------------");
                }
                int op;
                do{
                    op = sc("Digite a opção do cartão: ").nextInt();
                    if(op < 0 || op > cd.consultarCartao(cpf).size()){
                        System.out.println("Opção inválida.");
                    }
                }while(op < 0 && op > cd.consultarCartao(cpf).size());
                cd.consultarCartao(cpf);
                ArrayList<Cartao> l = cd.consultarCartao(cpf);
                if(l.size() > 0) {
                    //op = 0;
                    Cartao c = l.get(op);
                    op++;
                    System.out.println("-------------CARTÃO "+op+"--------------------");
                    System.out.println("Titular do cartão: "+cli.getNome());
                    System.out.println("Numero do cartão: "+c.getNumCartao());
                    System.out.println("Saldo: "+c.getSaldo());
                    System.out.println("Limite: "+c.getLimite());
                    System.out.println("Fatura: "+c.getPagamento());
                    op = 0;
                }else {
            	    System.out.println("Cliente não tem cartão.");
                }
            }

        }else{
            System.out.println("---------------------------------");
            System.out.println("Cliente não existe.");
        }
    }
    //===========================CONSULTA CLIENTE===================================
    public static void consultaClientes(){
        ClienteDAO cd = new ClienteDAO();
        ArrayList<Cliente> clientes = cd.consultarTodos();
        for(Cliente c: clientes) {
        	System.out.println("---------------------------------");
        	System.out.println("Nome: "+c.getNome());
        	System.out.println("CPF: "+c.getCpf());
        }
    }
    //===========================CONSULTA CARTAO===================================
    public static void consultaCartao(){
        String cpf = sc("Digite o CPF:\nDigite 0 para cancelar").next();
        Cliente cli = consultarPCPF(cpf);
        if(cli == null && !cpf.equals("0")){
            System.out.println("---------------------------------");
            System.out.println("Cliente não encontrado");
        }else if(cli != null){
            CartaoDAO cd = new CartaoDAO();
            ArrayList<Cartao> l = cd.consultarCartao(cpf);
            if(l.size() > 0) {
                int i = 1;
	            for(Cartao c: l){ 
	                System.out.println("-------------CARTÃO "+i+"--------------------");
	                System.out.println("Titular do cartão: "+cli.getNome());
	                System.out.println("Numero do cartão: "+c.getNumCartao());
	                System.out.println("Data de validade: "+c.getDataVal());
	                System.out.println("Código de segurança: "+c.getCodSeg());
	                System.out.println("Saldo: "+c.getSaldo());
	                System.out.println("Limite: "+c.getLimite());
	                System.out.println("Fatura: "+c.getPagamento());
                    i++;
	            }
            }else {
            	System.out.println("Cliente não tem cartão.");
            }
        }
    }

    public static Cliente consultarPCPF(String cpf){
        Cliente rt = null;
        ClienteDAO cd = new ClienteDAO();
        rt = cd.consultarPCPF(cpf);
        return rt;
    }

    public static void main(String[] args) throws Exception {
        int op;
        do{
            op = menu();
            switch(op){
                case 1:
                    cadastraCliente();
                break;
                case 2:
                    emiteCartao();
                break;
                case 3:
                    realizaTransacao();
                break;
                case 4:
                    pagaFatura();
                break;
                case 5:
                    consultaSaldo();
                break;
                case 6:
                    consultaClientes();
                break;
                case 7:
                    consultaCartao();
                break;
                case 8:
                System.out.println("Saindo do sistema...");
            }
        }while (op != 8);
        
    }
}
