const fs = require('fs');

function decodeValue(base, value) {
  let decoded = 0;
  for (let i = 0; i < value.length; i++) {
    decoded = decoded * base + parseInt(value[i], base);
  }
  return decoded;
}

function lagrangeBasis(i, x, xValues) {
  let result = 1;
  for (let j = 0; j < xValues.length; j++) {
    if (j !== i) {
      result *= (x - xValues[j]) / (xValues[i] - xValues[j]);
    }
  }
  return result;
}

function calculateConstantTerm(roots) {
  const xValues = roots.map(root => root.x);
  const yValues = roots.map(root => root.y);

  let constantTerm = 0;
  for (let i = 0; i < roots.length; i++) {
    constantTerm += yValues[i] * lagrangeBasis(i, 0, xValues);
  }

  return Math.round(constantTerm);
}

function processTestCase(filename) {
  const data = JSON.parse(fs.readFileSync(filename, 'utf-8'));
  const { k } = data.keys;

  const roots = Object.entries(data)
    .filter(([key]) => key !== 'keys')
    .map(([key, value]) => ({
      x: parseInt(key),
      y: decodeValue(value.base, value.value),
    }));

  return calculateConstantTerm(roots);
}

const testcase1Constant = processTestCase('testcase1.json');
const testcase2Constant = processTestCase('testcase2.json');

console.log("Testcase 1 Constant Term:", testcase1Constant);
console.log("Testcase 2 Constant Term:", testcase2Constant);
