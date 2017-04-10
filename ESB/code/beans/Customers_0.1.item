package beans;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import javax.xml.bind.annotation.XmlAccessType;
import javax.xml.bind.annotation.XmlAccessorType;
import javax.xml.bind.annotation.XmlElement;
import javax.xml.bind.annotation.XmlRootElement;


/**
 * <p>Java class for anonymous complex type.
 * 
 * <p>The following schema fragment specifies the expected content contained within this class.
 * 
 * <pre>
 * &lt;complexType>
 *   &lt;complexContent>
 *     &lt;restriction base="{http://www.w3.org/2001/XMLSchema}anyType">
 *       &lt;sequence>
 *         &lt;element name="customer" type="{http://talend.org//services/esb/registry/soap/2012/09}customer" maxOccurs="unbounded" minOccurs="0"/>
 *       &lt;/sequence>
 *     &lt;/restriction>
 *   &lt;/complexContent>
 * &lt;/complexType>
 * </pre>
 * 
 * 
 */
@XmlAccessorType(XmlAccessType.PROPERTY)
@XmlRootElement(name = "customers")
public class Customers {

	private Map<Long, Customer> customers = new HashMap<Long, Customer>();
	private long currentId = 1;
	
    public static class Entry extends Customer {
        public Long id;
    }

    public Customers() {
    };

    /**
     * Gets the value of the customer property.
     * 
     * <p>
     * This accessor method returns a reference to the live list,
     * not a snapshot. Therefore any modification you make to the
     * returned list will be present inside the JAXB object.
     * This is why there is not a <CODE>set</CODE> method for the customer property.
     * 
     * <p>
     * For example, to add a new item, do as follows:
     * <pre>
     *    getCustomer().add(newItem);
     * </pre>
     * 
     * 
     * <p>
     * Objects of the following type(s) are allowed in the list
     * {@link Customer }
     * 
     * 
     */
    @XmlElement(name="customer")
    public List<Entry> getCustomers() {
    	List<Entry> adaptedMap = new ArrayList<Entry>();
        for(Map.Entry<Long, Customer> mapEntry : customers.entrySet()) {
            Entry entry = new Entry();
            entry.id = mapEntry.getKey();
            entry.setFirstName(mapEntry.getValue().getFirstName());
            entry.setLastName(mapEntry.getValue().getLastName());
            entry.setCity(mapEntry.getValue().getCity());
            adaptedMap.add(entry);
        }
        return adaptedMap;
    }
    
    public void setCustomers(List<Entry> adaptedMap) {
        for(Entry entry : adaptedMap) {
            customers.put(entry.id, entry);
        }
    }

    public Customer getCustomer(long id) {
    	return customers.get(id);
    }

    public long addCustomer(Customer customer) {
    	customers.put(currentId, customer);
    	return currentId++;
    }
    
    public void updateCustomer(Long id, Customer customer) {
   		customers.put(id, customer);
    }
    
    public void deleteCustomer(long id) {
		customers.remove(id);
    }
    
}