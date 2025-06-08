import React, { useState } from "react";

const initialSpices = {
  "маскиратор": {
    ru: "глюкоза, соль, пряности (чеснок порошок, лук порошок, горчица порощок), усилитель вкуса и аромата (Е621), ароматизаторы, регулятор кислотности (Е330), дрожжевой экстракт, экстракты пряностей (мускатный орех, кориандр, тмин, перец черный, имбирь, чеснок, сельдерей), антикомкователь Е551",
    kg: "глюкоза, туз, татымалдар (кургатылган сарымсак, кургатылган пияз, кургатылган кычы), даам күчөткүч (Е621), жыт берүүчү заттар, кычкылдуулукту жөндөгүч (Е330), ачыткы экстракты, татымал экстракттары (мускат жаңгагы, кориандр, зире, кара мурч, имбирь, сарымсак, сельдерей), агломерацияга каршы зат Е551",
    kz: "глюкоза, тұз, дәмдеуіштер (сарымсақ ұнтағы, пияз ұнтағы, қыша ұнтағы), дәм мен хош иісті күшейткіш (E621), хош иістендіргіштер, қышқылдықты реттегіш (E330), ашытқы сығындысы, дәмдеуіштердің сығындылары (мускат жаңғағы, кориандр, зире, қара бұрыш, зімбір, сарымсақ, сельдерей), кесек түзілуге қарсы зат Е551"
  },
  "арома чеснока": {
    ru: "соль, крахмал, вещество вкусоароматическое натуральное (эфирное масло чеснока)",
    kg: "туз, крахмал, жыт берүүчү табигый зат (сарымсак эфир майы)",
    kz: "тұз, крахмал, табиғи дәм мен иіс заты (сарымсақтың эфир майы)"
  }
};

export default function SpiceLabelEditor() {
  const [spices, setSpices] = useState(initialSpices);
  const [newSpice, setNewSpice] = useState({ name: "", kg: "", ru: "", kz: "" });
  const [editKey, setEditKey] = useState(null);
  const [selectedSpices, setSelectedSpices] = useState([]);

  const handleChange = (e) => {
    const { name, value } = e.target;
    setNewSpice({ ...newSpice, [name]: value });
  };

  const handleAddOrUpdate = () => {
    if (!newSpice.name.trim()) return;
    const updatedSpices = { ...spices };
    updatedSpices[newSpice.name.trim()] = {
      kg: newSpice.kg.trim(),
      ru: newSpice.ru.trim(),
      kz: newSpice.kz.trim()
    };
    setSpices(updatedSpices);
    setNewSpice({ name: "", kg: "", ru: "", kz: "" });
    setEditKey(null);
  };

  const handleEdit = (key) => {
    setNewSpice({ name: key, ...spices[key] });
    setEditKey(key);
  };

  const handleDelete = (key) => {
    const updated = { ...spices };
    delete updated[key];
    setSpices(updated);
  };

  const toggleSelectSpice = (key) => {
    setSelectedSpices((prev) =>
      prev.includes(key) ? prev.filter((k) => k !== key) : [...prev, key]
    );
  };

  const generateComposition = (lang) => {
    const compositions = selectedSpices.map((key) => spices[key][lang]);
    return compositions.join(", ");
  };

  return (
    <div className="p-4 space-y-4">
      <h2 className="text-xl font-bold">Добавление/Редактирование специй</h2>
      <div className="grid grid-cols-1 md:grid-cols-2 gap-2">
        <input
          name="name"
          value={newSpice.name}
          onChange={handleChange}
          placeholder="Название специи"
          className="border p-2 rounded"
        />
        <textarea
          name="kg"
          value={newSpice.kg}
          onChange={handleChange}
          placeholder="Состав на кыргызском"
          className="border p-2 rounded"
        />
        <textarea
          name="ru"
          value={newSpice.ru}
          onChange={handleChange}
          placeholder="Состав на русском"
          className="border p-2 rounded"
        />
        <textarea
          name="kz"
          value={newSpice.kz}
          onChange={handleChange}
          placeholder="Состав на казахском"
          className="border p-2 rounded"
        />
      </div>
      <button
        onClick={handleAddOrUpdate}
        className="bg-blue-600 text-white px-4 py-2 rounded"
      >
        {editKey ? "Обновить специю" : "Добавить специю"}
      </button>

      <h3 className="text-lg font-semibold mt-6">Текущие специи:</h3>
      <ul className="space-y-2">
        {Object.keys(spices).map((key) => (
          <li
            key={key}
            className={`border p-2 rounded shadow cursor-pointer ${
              selectedSpices.includes(key) ? "bg-green-100" : ""
            }`}
            onClick={() => toggleSelectSpice(key)}
          >
            <div className="font-bold">{key}</div>
            <div><strong>Кыргызча:</strong> {spices[key].kg}</div>
            <div><strong>Русский:</strong> {spices[key].ru}</div>
            <div><strong>Қазақша:</strong> {spices[key].kz}</div>
            <div className="space-x-2 mt-2">
              <button
                onClick={(e) => {
                  e.stopPropagation();
                  handleEdit(key);
                }}
                className="bg-yellow-500 text-white px-3 py-1 rounded"
              >
                Редактировать
              </button>
              <button
                onClick={(e) => {
                  e.stopPropagation();
                  handleDelete(key);
                }}
                className="bg-red-600 text-white px-3 py-1 rounded"
              >
                Удалить
              </button>
            </div>
          </li>
        ))}
      </ul>

      <div className="mt-6">
        <h3 className="text-lg font-semibold">Автосостав:</h3>
        <div className="mt-2 space-y-2">
          <div><strong>Кыргызча:</strong> {generateComposition("kg")}</div>
          <div><strong>Русский:</strong> {generateComposition("ru")}</div>
          <div><strong>Қазақша:</strong> {generateComposition("kz")}</div>
        </div>
      </div>
    </div>
  );
}
